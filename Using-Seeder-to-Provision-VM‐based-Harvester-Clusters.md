The Harvester team often uses the [ipxe-examples](https://github.com/harvester/ipxe-examples) to spawn up live VM-based clusters for development and testing purposes. This wiki introduces an alternative way of doing it. By leveraging the [VirtualBMC project](https://opendev.org/openstack/virtualbmc), it's possible to use [Seeder](https://github.com/harvester/seeder) to provision Harvester clusters with virtual machines.

Below is the reference architecture of the setup:

![image](https://github.com/harvester/harvester/assets/1827717/1ac87bcb-f059-4f85-a40e-832553c42490)

Prerequisites
-------------

We need the following packages and tools installed to be able to proceed:

- KVM/QEMU - needless to say, we need these
- Libvirt - RKE2 and Harvester nodes will be created using libvirt APIs
- virt-install - for creating domain definitions (XMLs)
- Nginx - for Harvester artifacts store (using `python3 -m http.server` would work, too)
- noVNC - for accessing the VM consoles (optional)

Environment Information
-----------------------

- Hypervisor
  - IP address: 192.168.48.111/24
- Networking (bridge mode)
  - Bridge name: `br0`
  - Subnet: 192.168.48.0/24
  - Gateway: 192.168.48.1

Kubernetes Setup
----------------

Any Kubernetes cluster would work. It's okay to skip this section if you already have one. The purpose is to prepare an environment for installing Seeder and other relevant charts. Here we deploy an RKE2 cluster using Terraform for the completeness of the demonstration.

```sh
git clone https://github.com/starbops/terraform-rke2.git
cd terraform-rke2/
make tf-init
make tf-plan
make tf-apply
```

A kubeconfig file will be downloaded under the default `/tmp/rke2` directory. You may want to specify the path of the kubeconfig file while you use commands like `kubectl` and `helm`:

```sh
export KUBECONFIG=/tmp/rke2/rke2.yaml
```

Seeder Setup
------------

Deploy Seeder on the cluster. Since we need the provisioning capability of Seeder, it's required to turn off the `embeddedMode` flag to make it run in standalone mode.

```sh
helm repo add harvester https://charts.harvesterhci.io
helm repo update
helm upgrade --install harvester-seeder harvester/harvester-seeder --set=embeddedMode=false --namespace=seeder-system --create-namespace
```

Besides the Seeder itself, we also need Tinkerbell Boots. It's required for Seeder to provision Harvester clusters.

Note: We need a custom-built boots container image for Harvester provisioning to work. Hopefully, this will no longer be required in the future.

```sh
git clone https://github.com/harvester/seeder.git
cd seeder/
helm upgrade --install boots chart/seeder/charts/boots --namespace=seeder-system --create-namespace
```

Harvester VM Setup
------------------

Create an empty VM for iPXE boot later. This will also create storage.

Note: The MAC address should be identical to the one specified in the corresponding Inventory CR later.

```sh
virt-install \
  --name=harvester-vm \
  --os-variant="sles12sp5" \
  --vcpus=8 \
  --memory=16384 \
  --boot=network,hd \
  --disk=path="$HOME"/libvirt/images/harvester-vm-disk-0.img,bus=virtio,size=240 \
  --network=bridge=br0,model=virtio,mac=52:54:00:01:00:01 \
  --graphics=vnc \
  --noautoconsole \
  --print-xml | virsh define /dev/stdin
```

VirtualBMC Setup
-----------------

Install VirtualBMC in a Python virtual environment.

```sh
mkdir virtualbmc
cd virtualbmc
python3 -m venv .venv
source .venv/bin/active
python -m pip install --upgrade pip wheel
pip install virtualbmc
```

Start the VirtualBMC daemon.

```sh
vbmcd --foreground
```

Create and start a virtual BMC associated with the VM we just created. This will make the daemon listen on the specified port receiving IPMI requests for the VM.

```sh
vbmc add \
  --username admin \
  --password password \
  --port 10623 \
  --address 192.168.48.111 \
  --libvirt-uri qemu:///system \
  harvester-vm

vbmc start harvester-vm
```

Make sure the virtual BMC is in `running` status and the address/port tuple is correct:

```sh
$ vbmc list
+--------------+---------+----------------+-------+
| Domain name  | Status  | Address        |  Port |
+--------------+---------+----------------+-------+
| harvester-vm | running | 192.168.48.111 | 10623 |
+--------------+---------+----------------+-------+
```

Let's verify it by using the following command:

```sh
$ ipmitool -I lanplus -H 192.168.48.111 -U admin -P password -p 10623 power status
Chassis Power is off
```

The VM should be off. Later on, it will be booted up by Seeder.

Provision Harvester
-------------------

Prepare the following manifest files:

- AddressPool CRs: One for the nodes, the other for the VIP
- Secret: The credentials for the virtual BMC
- Inventory CR: The VM
- Cluster CR: The Harvester cluster

```yaml
---
apiVersion: metal.harvesterhci.io/v1alpha1
kind: Cluster
metadata:
  name: foxtrot
  namespace: default
spec:
  version: "v1.1.2"
  imageURL: "http://192.168.48.111:8000/harvester"
  clusterConfig:
    nameservers:
    - 1.1.1.1
    - 8.8.4.4
  nodes:
  - inventoryReference:
      name: harvester-vm
      namespace: default
    addressPoolReference:
      name: node-pool
      namespace: default
  vipConfig:
    addressPoolReference:
      name: vip-pool
      namespace: default
---
apiVersion: metal.harvesterhci.io/v1alpha1
kind: Inventory
metadata:
  name: harvester-vm
  namespace: default
spec:
  primaryDisk: /dev/vda
  managementInterfaceMacAddress: "52:54:00:01:00:01"
  baseboardSpec:
    connection:
      authSecretRef:
        name: vms
        namespace: default
      host: 192.168.48.111
      insecureTLS: true
      port: 10623
  events:
    enabled: false
    pollingInterval: 1h
---
apiVersion: metal.harvesterhci.io/v1alpha1
kind: AddressPool
metadata:
  name: node-pool
  namespace: default
spec:
  cidr: "192.168.48.224/28"
  gateway: "192.168.48.1"
  netmask: "255.255.255.0"
---
apiVersion: v1
data:
  password: cGFzc3dvcmQ=
  username: YWRtaW4=
kind: Secret
metadata:
  creationTimestamp: null
  name: vms
  namespace: default
---
apiVersion: metal.harvesterhci.io/v1alpha1
kind: AddressPool
metadata:
  name: vip-pool
  namespace: default
spec:
  cidr: "192.168.48.240/28"
  gateway: "192.168.48.1"
  netmask: "255.255.255.0"
```

Create the above resources using `kubectl apply`, and Seeder will handle the rest.

<img width="1023" alt="image" src="https://github.com/harvester/harvester/assets/1827717/434fcd23-9a96-460c-9fbb-311d355ed366">

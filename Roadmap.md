v1.0.2 (May 2022)
------
* [Upgrade from v1.0.1](https://github.com/harvester/harvester/issues/1861)
* [Support bundle for a single node](https://github.com/harvester/harvester/issues/1864)
* [Legacy Iso for older servers](https://github.com/harvester/harvester/issues/2023)
* [Enhancement of qemu-guest-agent design](https://github.com/harvester/harvester/issues/2002)
* [WebUI adds setting of monitoring-prometheus-node-exporter](https://github.com/harvester/harvester/issues/2075)
* [Harvester NODE IP, VIP enhancement](https://github.com/harvester/harvester/issues/1683)

v1.1.0
------
* Support seamless migration from other virtualization platforms.
* [VM Snapshot/revert](https://github.com/rancher/harvester/issues/553)
* [VM clone based on in-cluster snapshot](https://github.com/rancher/harvester/issues/569)
* Storage Network
* [Log collection and export](https://github.com/rancher/harvester/issues/577)
  *   Syslog forwarding of hypervisor host logs
* [Event recording](https://github.com/rancher/harvester/issues/578)
* SR-IOV
* GPU passthrough support.
* VXLAN
* Enhanced Cloud Provider support.
* Enhanced Windows experience
* Restore Harvester VMs into a new Harvester cluster.
* Support configure Harvester cluster after installation (https://github.com/harvester/harvester/issues/2198)
* Enhanced image management


Future
------
* Container and VM network integration or path to communication 
* Enhanced Integration with Rancher for massive edge deployments
* Network enhancement: support telco network solutions
* VLAN for mgmt network
* Hardware error detection


Past releases
---

Harvester v1.0.1 (Apr 2022)
---------------------

* [Upgrade from v1.0.0](https://docs.harvesterhci.io/v1.0/upgrade/automatic/)
* [Harvester RKE2 node driver enhancement](https://github.com/harvester/harvester/issues?q=is%3Aissue+label%3Aarea%2Fnode-driver+milestone%3Av1.0.1+)
* [VM soft reboot/shutdown](https://github.com/harvester/harvester/issues/574)
* [Allow using additional data disks during installation](https://github.com/harvester/harvester/issues/1728)
* [Windows Server (2019 & 2022) Support Enhancement](https://github.com/harvester/harvester/issues?q=is%3Aissue+windows+label%3Aarea%2Fwindows+milestone%3Av1.0.1+)
* [Load Balancer Enhancement of the Harvester node driver cluster](https://github.com/harvester/harvester/issues?q=is%3Aissue+label%3Aarea%2Fload-balancer+milestone%3Av1.0.1+)

Harvester v1.0.0 GA (Dec 2021)
---------------------

*   Stabilization

Harvester v0.2.0 (Apr 2021, Beta)
-----------------------

*   [VM Live Migration](https://github.com/rancher/harvester/issues/384)
*   [VM backup/restore](https://github.com/harvester/harvester/issues/385)
*   [Backing image support to shorten the VM starting time](https://github.com/rancher/harvester/issues/227)
*   [PXE support](https://github.com/rancher/harvester/issues/217) 
    *   iPXE on Packet
*   [Zero downtime upgrade](https://github.com/rancher/harvester/issues/383)
    *   **NOTE: Due to pending OS changes in the future, the upgrade might be unsupported until GA release.**
*   [Per node NIC configuration](https://github.com/harvester/harvester/issues/369)
*   [Install Rancher with Harvester](https://github.com/rancher/harvester/issues/513)
*   [Provision Kubernetes cluster using Harvester and Rancher](https://github.com/rancher/harvester/issues/512)  
*   [Support bundle](https://github.com/rancher/harvester/issues/579)

Harvester v0.3.0 (October, 2021)
------------------

*   [End-to-end test framework](https://github.com/rancher/harvester/issues/551)
*   [Harvester/Rancher UI interaction improvement](https://github.com/harvester/harvester/issues/755)
    *   Central view into VM data within Rancher's VUE UI or some other UI equivalent
*   [Harvester as Kubernetes Cloud Provider](https://github.com/rancher/harvester/issues/633)
    *   Including [Built-in HA load balancer](https://github.com/rancher/harvester/issues/632)
*   [Harvester as Terraform Provider](https://github.com/rancher/harvester/issues/634)
*   [Hot-plug disk support](https://github.com/rancher/harvester/issues/283)
    *   Including live migration
*   [Guest Kubernetes cluster CSI driver](https://github.com/rancher/harvester/issues/580)
    *   Provide bare metal storage via Longhorn to the VM Kubernetes clusters inside the Harvester.
*   [Multi-tenant](https://github.com/rancher/harvester/issues/582)
    *   [Including Rancher authentication integration](https://github.com/rancher/harvester/issues/552)
*   [Node eviction](https://github.com/rancher/harvester/issues/573)
*   [Replace K3OS](https://github.com/rancher/harvester/issues/581)
*   [Monitoring dashboard and endpoint for Prometheus](https://github.com/rancher/harvester/issues/576)
    *   Including real-time storage performance metrics 
*   [Template from VM including root and data disk](https://github.com/rancher/harvester/issues/571)
*   [Image upload](https://github.com/rancher/harvester/issues/570)
*   [Create image from volume](https://github.com/rancher/harvester/issues/568)
*   [Multidisk management](https://github.com/harvester/harvester/issues/747)
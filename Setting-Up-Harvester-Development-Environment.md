This page will go over setting up your environment for development work on the current version of the Harvester. This is primarily built on the premise of setting up in a MacOS environment, but most of it should carry over to Linux as well.

We're using ZenHub for project management. Install [ZenHub Chrome Extension](https://chrome.google.com/webstore/detail/zenhub-for-github/ogcgkffhplmphkaahpmffcafajaocjbd?hl=en-US) to see the project management view in GitHub.

#### Table of contents

1. [Install Dependencies](#install-dependencies)
    - [Go](#go)
2. [Install Harvester](#install-harvester)
    - [HCI Mode](#hci-moderecommended)
    - [Helm Mode](#helm-mode)
3. [IDE](#ide)
4. [Harvester Repos](#harvester-repos)
5. [Set up GoLand](#set-up-goland)
6. [Scale Harvester Pod Down to 0](#scale-the-harvester-pod-down-to-0)
7. [Run](#run)

## Install Dependencies
### Go

Harvester is built in Go, which can be downloaded here: https://golang.org/dl/. Alternatively, on macOS it can be installed with [homebrew](https://brew.sh/) using the following command: `brew install go`

additionally, you should add the `GOPATH` bin directory to your path. Add the following to your .bashrc or other shell files

```console
export PATH=${GOPATH//://bin:}/bin:$PATH
```
  
  
## Install Harvester

### HCI Mode(Recommended)

You should be able to download the latest Harvester ISO from [here](https://releases.rancher.com/harvester/master/harvester-amd64.iso). The CI in [rancher/harvester-installer](https://github.com/rancher/harvester-installer) repo will auto-build the newest ISO image upon every merged PRs, it will also run a daily cronjob at 00:00 am (UTC) to build a daily newest ISO image.

For `initrd` and `vmlinuz` files they are available at:
- [harvester-initrd-amd64](https://releases.rancher.com/harvester/master/harvester-initrd-amd64)
- [harvester-vmlinuz-amd64](https://releases.rancher.com/harvester/master/harvester-vmlinuz-amd64)


#### Option 1) On the Bare-metal Server

Follow the [ISO installation](https://github.com/rancher/harvester/blob/master/docs/iso-installation.md) guideline to set up your Harvester server, and get the `KubeConfig` file from the management node at `/etc/rancher/k3s/k3s.yaml`.


#### Option 2) Using Vagrant


#### Option 3) Using VirtualBox

Download and install the [Virtualbox 6.x](https://www.virtualbox.org/), click to create a new VM with the following configuration:
 - choose `Type: Linux` and `Version: Other Linux(64-bit)`
 - set memory size to a minimum of 8192MB
 - set disk size to a minimum of 200GB
 - clicking the `Settings` of the created VM and navigate to the `System > Processor` to set the CPU to a minimum of 4 cores, and enable the `PAE/NX` option, and set the nested virtualization via terminal with:
```console
VBoxManage modifyvm "$custom-name" --nested-hw-virt on
```
 - go to the networks panel and enable the second network adaptor with either attached to the `Host-only Adaptor` or any other type of adaptor that can be accessed from your local network later.
 - lastly, follow the [ISO installation](https://github.com/rancher/harvester/blob/master/docs/iso-installation.md) and install the Harvester to your VM.
 - optionally you can create more VMs to form a cluster.

### Helm Mode

The local Harvester instance will be hosted in an installation of Kubernetes. Typically this will be done with a local cluster, but you can also launch one in the cloud and simply point your Kubeconfig there. The easiest way to spin up a k8s cluster in the cloud would be using the Rancher's RKE or k3s:
 - [Rancher](https://rancher.com/docs/rancher/v2.x/en/)
 - [k3s](https://k3s.io/)

App mode installation guidance would be available at [here](https://github.com/rancher/harvester/blob/master/docs/app-mode-installation.md), please be aware that the Kubernetes node must have hardware virtualization support to test the VM features in the Harvester.
    
    
## IDE

Most of us at the Harvester team uses GoLand, and the rest of this document will be written assuming you are as well, but there are several options
 - [GoLand](https://www.jetbrains.com/go/)
 - [vim + vim-go](https://github.com/fatih/vim-go)
 - [VS Code + vscode-go](https://github.com/Microsoft/vscode-go)
 - [Atom + go-plus](https://atom.io/packages/go-plus)

## Harvester Repos
As of Harvester development, you will likely only need the [rancher/harvester](https://github.com/rancher/harvester). But here is a list of available reports of its related development usage.

- [Harvester UI](https://github.com/rancher/harvester-ui) - Harvester UI
- [Harvester Network Controller](https://github.com/rancher/harvester-network-controller) - Harvester network controller of managing additional networks like VLAN
- [Harvester Installer](https://github.com/rancher/harvester-installer) - Harvester installer for building the ISO image
- [Harvester Node Driver](https://github.com/harvester/docker-machine-driver-harvester) - Harvester node driver based on the Docker machine
- [GO Harvester](https://github.com/rancher/go-harvester) - a Go client library for the Harvester

### Setting up remotes
To avoid accidentally creating a branch in the rancher/harvester repo please set your remote accordingly with your fork. 

```console
origin    git@github.com:AwesomeContributor/harvester.git (fetch)
origin    git@github.com:AwesomeContributor/harvester.git (push)
upstream    https://github.com/rancher/harvester.git (fetch)
upstream    https://github.com/rancher/harvester.git (push)
```

HTTPS URLs allow for unauthenticated cloning and pulling. To get the most up-to-date version run:
```console
git fetch upstream
```

## Set up GoLand
As discussed above, we primarily use GoLand for development. There are a few things to do here

### Set go mod or GOPATH
* If using a version of the Harvester on `go mod`, in `preferences -> Go -> Go Modules` select "Enable Go Modules" and also "Vendoring mode". 
* If not using go mod, the GOPATH for a given project should be one step up from the `src` folder. Set this in `preferences -> Go -> GOPATH -> Project GOPATH`. 

### Set up the build target
Click the `Run` menu option and select `Edit Configurations`. Click the + to add a template and select `Go Build`

  - In files select the main.go file.
  - add environment variable `KUBECONFIG` set to `{homedirectory}/.kube/config` (on mac `/Users/<username>/.kube/config`).
  - add env variable `NAMESPACE` set to `harvester-system` (the default namespace to store management resources).
  - (optional) add env variable `HARVESTER_AUTHENTICATION_MODE` set to `localUser` if you are not testing with Rancher integration
  - set env variable `HARVESTER_AUTHENTICATION_MODE: rancher`, `RANCHER_EMBEDDED: true` and `RANCHER_SERVER_URL: rancher-server-url=https://cluster_url:30444` if you are testing Rancher integration mode.
  - set env variable `HCI_MODE: true` if you are testing HCI mode controllers, e.g zero-downtime upgrade

![Imgur](https://i.imgur.com/mZIiUr2.png)


## Scale the Harvester pod down to 0
By default, there are 3 Harvester pod get deployed with the Harvester installation, you will need to scale the pod number down to 0 to test the controller logic at `pkg/controller/master`:
```
kubectl scale --replicas=0 deployment/harvester -n harvester-system
```
if you need testing the HA scenario, you can scale the pod number back to 2 and modify the code in `pkg/controller/global` accordingly.

## Run!
Start the built-in GoLand. The service will be available at `https://localhost:8443`. Ignore the scary cert warning.
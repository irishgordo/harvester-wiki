> [!Important]
> The Roadmap presented herein outlines our tentative plans, which are subject to modification based on various factors over time. This document should not be construed as a binding commitment to delivery. Rather, its primary purpose is to provide insight into our intended objectives and strategic direction.
> 
> For tentative planned release schedules, please refer to the milestones page at https://github.com/harvester/harvester/milestones.
>
> Regarding the release cadence, please refer to [Release Cadence](https://github.com/harvester/harvester/wiki/Release-Cadence-%26-Support). 
> In general, there are three minor releases per year, occurring in _**April, August, and December**_.

This page primarily outlines the [upcoming release roadmap](#upcoming-releases) and highlighted items in the [product backlog](#product-backlog). For previous releases, please refer to [[Release History]].

# Upcoming Releases

## Harvester v1.7.0

> [!Important]
> The scope has not been finalized yet. The items below are tentatively planned for the release.

* Dependent Component Upgrade
  * K8s - RKE2 v1.34
  * Longhorn - v1.10
  * Rancher - v2.13
  * KubeVirt - v1.6
  * SLE Micro - v6.1/6.2
* [Update/upgrade improvements](https://github.com/harvester/harvester/issues/4926)
* [Auto Live Migration (rebalance VM workloads)](https://github.com/harvester/harvester/issues/2311)
* [Add support for MIG-based vGPUs in Harvester](https://github.com/harvester/harvester/issues/8652)
* [Bump base OS to SLE Micro 6](https://github.com/harvester/harvester/issues/7025)
* [Stand-alone update manager](	https://github.com/harvester/harvester/issues/7112)
* [Hotplug resources (CDROM, and vNIC)	](https://github.com/harvester/harvester/issues/8283)
* [Bare-Metal containers feature (switching vCluster to k3k)](https://github.com/harvester/harvester/issues/5820)
* [Observatibilty extension compatibility](https://github.com/harvester/harvester/issues/8282)
* [Pause-ability of node upgrades](https://github.com/harvester/harvester/issues/8980)
* [[EPIC] 3rd party storage enhancements](https://github.com/harvester/harvester/issues/8026)
* [Allow shared disk between VMs (non-Longhorn)	](https://github.com/harvester/harvester/issues/8422)
* [Pod Security Admission (PSA) support	](https://github.com/harvester/harvester/issues/8196)
* [Hardening Guidance](https://github.com/harvester/harvester/issues/8792)
* [Hugepage support](https://github.com/harvester/harvester/issues/5006)
* [CPU Masking	](https://github.com/harvester/harvester/issues/3015)
* [VM Trunking (support multiple/all VLANs to a VM)](https://github.com/harvester/harvester/issues/766)
* [VM Import - support OVF import](https://github.com/harvester/harvester/issues/4742)
* [Support Volume Snapshots in the harvester-csi-driver](https://github.com/harvester/harvester/issues/3778)
* [Add hugepage support to Harvester](https://github.com/harvester/harvester/issues/5006)
* [Add VLAN network for VM to support multiple or all VLAN IDs](https://github.com/harvester/harvester/issues/766)

# Planned Release

## Harvester v1.8.0

> [!Important]
> The scope has not been finalized yet. The items below are tentatively planned for the release.

* Dependent Component Upgrade
  * K8s - RKE2 v1.35
  * Longhorn - v1.11
  * Rancher - v2.14
  * KubeVirt - v1.7
  * SLE Micro - v6.1/6.2

## Harvester v1.9.0

> [!Important]
> The scope has not been finalized yet. The items below are tentatively planned for the release.

* Dependent Component Upgrade
  * K8s - RKE2 v1.36
  * Longhorn - v1.12
  * Rancher - v2.15
  * KubeVirt - v1.8
  * SLE Micro - v6.1/6.2

# Product Backlog
To be updated soon

# Previous Releases

## Harvester v1.6.0

* Dependent Component Upgrade
  * K8s - RKE2 v1.33
  * Longhorn - v1.9.1
  * Rancher - v2.12.0
  * KubeVirt - v1.5.2
  * SLE Micro - v5.5
* [Kube-OVN integration for SDN](https://github.com/harvester/harvester/issues/7397) - _**Experimental**_ 
  * [Kube-OVN install operator](https://github.com/harvester/harvester/issues/7936)
  * [Virtual Switch/Router](https://github.com/harvester/harvester/issues/7332)
  * [Network microsegmentation via network policy](https://github.com/harvester/harvester/issues/7381)
  * [VXLAN support](https://github.com/harvester/harvester/issues/2322)
* [3rd-party storage enhancements](https://github.com/harvester/harvester/issues/8026)
  * [Extend vm-import-controller to support 3rd party CSI](https://github.com/harvester/harvester/issues/8074)
  * [3rd party CSI support for guest clusters provisioned by rancher](https://github.com/harvester/harvester/issues/8075)
  * [Harvester CSI changes for supporting 3rd party CSI volumes for guest workloads](https://github.com/harvester/harvester/issues/8076)
  * [Expose CDI settings in Harvester to allow fine-tuning CDI settings for day 2 operations](https://github.com/harvester/harvester/issues/8077)
  * [Online resize of disks/volumes attached to virtual machines](https://github.com/harvester/harvester/issues/2811)
* [Airgap Upgrade via ISO upload](https://github.com/harvester/harvester/issues/8078)
* [Live migration network](https://github.com/harvester/harvester/issues/5848)
* [Live migration progress awareness](https://github.com/harvester/harvester/issues/4352)
* [Longhorn v1 data engine - volume encryption GA](https://github.com/harvester/harvester/issues/8148)
* [CPU / Memory hotplug support](https://github.com/harvester/harvester/issues/5835)
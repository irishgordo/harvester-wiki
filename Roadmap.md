> [!Important]
> The Roadmap presented herein outlines our tentative plans, which are subject to modification based on various factors over time. This document should not be construed as a binding commitment to delivery. Rather, its primary purpose is to provide insight into our intended objectives and strategic direction.
> 
> For tentative planned release schedules, please refer to the milestones page at https://github.com/harvester/harvester/milestones.
>
> Regarding the release cadence, please refer to [Release Cadence](https://github.com/harvester/harvester/wiki/Release-Cadence-%26-Support). 
> In general, there are three minor releases per year, occurring in _**April, August, and December**_.

This page primarily outlines the [upcoming release roadmap](#upcoming-releases) and highlighted items in the [product backlog](#product-backlog). For previous releases, please refer to [[Release History]].

# Upcoming Releases

## Harvester v1.4.3

* Dependent Component Upgrade
  * K8s - v1.31.7+rke2r1
  * Longhorn - v1.7.3
  * Rancher - v2.10.3
  * KubeVirt - v1.3.1
  * SLE Micro - v5.5

## Harvester v1.5.1

* Dependent Component Upgrade
  * K8s - v1.32.4
  * Longhorn - v1.8.2
  * Rancher - v2.11.2 (embedded & external)
  * KubeVirt - v1.4.0
  * SLE Micro - v5.5

## Harvester v1.6.0

> [!Important]
> The scope has not been finalized yet. The items below are tentatively planned for the release.

* Dependent Component Upgrade
  * K8s - v1.33
  * Longhorn - v1.9
  * Rancher - v2.12 (embedded & external)
  * KubeVirt - v1.5
  * SLE Micro - v5.5
* [Kube-OVN integration for SDN](https://github.com/harvester/harvester/issues/7397)
  * [Kube-OVN install opreator](https://github.com/harvester/harvester/issues/7936)
  * [Network microsegregation via network policy](https://github.com/harvester/harvester/issues/7381)
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
* [Add hugepage support to Harvester](https://github.com/harvester/harvester/issues/5006)
* [Allow adding other endpoint to tls-san in RKE2 config](https://github.com/harvester/harvester/issues/7716)
* [Add VLAN network for VM to support multiple or all VLAN IDs](https://github.com/harvester/harvester/issues/766)

## Harvester v1.7.0

> [!Important]
> TBU

## Harvester v1.8.0

> [!Important]
> TBU

# Product Backlog

* Backup/restore of 3rd-party storage
* Change Longhorn resource reservations for customers only using 3rd party storage
* Observability Extension (Tech Preview)
* [Allow upgrade when there are VMs in the cluster that can't be Live Migrated](https://github.com/harvester/harvester/issues/6145)
* Storage Live Migration
* [DR Volume](https://github.com/harvester/harvester/issues/1850)
* SLE Micro 6.x Upgrade
* [Managed DHCP add-on](https://github.com/harvester/harvester/issues/6746) (Preview)
* [Cluster Backup & Restore](https://github.com/harvester/harvester/issues/3263)
* [Harvester install in maintenance mode](https://github.com/harvester/harvester/issues/4424)
* [VM DHCP Controller GA](https://github.com/harvester/harvester/issues/5822)
* [Baremetal Container Workload Support GA](https://github.com/harvester/harvester/issues/5820)
* Dynamic VM rebalancing across hosts
* Shared Disk between VMs
* [Load Balancers on Non-Management Interfaces for Guest Clusters](https://github.com/harvester/harvester/issues/5486)
* [Longhorn v2 data engine - volume encryption](https://github.com/harvester/harvester/issues/7523)

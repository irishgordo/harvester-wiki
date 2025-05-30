# Harvester & Rancher Support Matrix

This matrix outlines the compatibility between different versions of Harvester and Rancher. Since Harvester and Rancher are developed and released independently, it's important to ensure version alignment for full feature support and stable integration. Use this matrix to determine which Rancher versions are fully compatible with specific Harvester releases

RKE1 is now EOL (end of life) on Harvester v1.5.0. You can find more specifics and recommended steps [here](https://www.suse.com/support/kb/doc/?id=000021513).

### Upgrading guidance

When upgrading from Harvester v1.4.2 to v1.5.x, it is recommended to upgrade Rancher first. Then you can upgrade Harvester.

To access the Harvester GUI within Rancher, UI extension must be installed. Please make sure to install UI extension version supported with Harvester & Rancher. During an upgrade, users can continue using the existing UI extension until Rancher has been upgraded. Rancher is expected to function correctly with the older UI extension throughout the upgrade process. However, to access new features introduced in the latest Harvester release, you must update the UI extension to the version compatible with your upgraded Rancher and Harvester setup.

It's important to note that the `harvester-ui-extension`  version is only supported on specific versions of Harvester and Rancher as outlined in the chart below.
| Harvester Version | Rancher Version | Harvester Node Driver Supported k8s Versions | UI Extension |
| ----------------- | --------------- | -------------------------------------------- | ------------ |
|v1.5.0             | v2.11.0	      |  RKE2 v1.30, v1.31, v1.32                    | 	1.5.0       |

## Harvester CCM & CSI Drivers
CCM and CSI drivers support RKE1, RKE2 and k3s distributions, unless otherwise noted.

For Harvester CCM and CSI driver integration with RKE1, please install or upgrade to the latest chart manually from the Catalog Apps. Refer to the Harvester doc here for more details.

| Harvester Cloud Provider | Harvester CSI Driver | RKE2 Version | Feature Upgrade Support |
| ------------------------ | -------------------- | -------------------------------------------- | ------------ |
| v0.2.9                   |	v0.1.23           | >=[v1.30.10+rke2r1](https://github.com/rancher/rke2/releases/tag/v1.30.10%2Brke2r1), >=[v1.31.6+rke2r1](https://github.com/rancher/rke2/releases/tag/v1.31.6%2Brke2r1), >=[v1.32.3+rke2r1](https://github.com/rancher/rke2/releases/tag/v1.32.3%2Brke2r1) | Yes |
## Other Dependency Versions

| Harvester Version | Harvester Terraform Provider |
| ----------------- | ---------------------------- |
| v1.5.0 | [v0.6.7](https://github.com/harvester/terraform-provider-harvester/releases/tag/v0.6.7) |


## Guest Operating System Support
Full support for the following guest operating systems that have been validated to run in Harvester: 

| OS Family | Versions |
| --------- | -------- |
| SLES | 15 SP6 |
| OpenSUSE Leap | 15.6 |
| SLE Micro | 6 |
| Ubuntu | 24.04 |

All other x86 operating systems are supported on a "best effort" basis. 

## Hardware Requirements
Harvester is built on SLE technology. To get the Harvester server up and running, the following minimum hardware is required:  

| Type | Requirements |
| ---- | ------------ |
| Cluster |	Minimum of 3 servers in each cluster |
| CPU |	x86_64 only. Hardware-assisted virtualization is required. 8-core processor minimum for testing; 16-core or above is required |
| Memory |	32 GB minimum, 64 GB or above required |
| Disk Capacity |	200 GB minimum for testing (180 GB minimum when using multiple disks); 500 GB or above is required for production. |
| Disk Performance |	5,000+ random IOPS per disk(SSD/NVMe). Management nodes (first 3 nodes) must be [fast enough for Etcd](https://www.suse.com/support/kb/doc/?id=000020100). Only local disks or Hardware RAID is supported. |
| Network Card |	1 Gbps Ethernet minimum for testing, 10Gbps Ethernet minimum required for production.  |
| Network Switch |	Trunking of ports required for VLAN support |

Testing environments with 1 GB NIC / 140 GB disk are not supported

*We recommend server-class hardware for the best results. Laptops and nested virtualization are not commercially supported*
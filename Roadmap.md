> [!Important]
> The Roadmap presented herein outlines our tentative plans, which are subject to modification based on various factors over time. This document should not be construed as a binding commitment to delivery. Rather, its primary purpose is to provide insight into our intended objectives and strategic direction.
> 
> For precise release schedules, please refer to the milestones page at https://github.com/harvester/harvester/milestones.

v1.3.2 (August 2024)
-----
* Dependent Component Upgrade
  * K8s - v1.28
  * Longhorn - v1.6.2
  * Rancher - v2.8.4
  * KubeVirt - v1.1.1
  * SLE Micro - 5.4
* Stabilization
* Security patches

v1.4.0 (October 2024)
-----
* Dependent Component Upgrade
  * K8s - v1.29
  * Longhorn - v1.7.0
  * Rancher - TBU (v2.9.x)
  * KubeVirt - v1.2.2
  * SLE Micro - 5.5
* [Local volume support w/o live migration](https://github.com/harvester/harvester/issues/5724) (Experimental)
* [RWX volume for Guest Cluster Workload](https://github.com/harvester/harvester/issues/1992)
* [Restore Harvester VMs into a new Harvester cluster](https://github.com/harvester/harvester/issues/2237)
* [Scheduling of VM Backups](https://github.com/harvester/harvester/issues/2756)
* [CPU Pinning](https://github.com/harvester/harvester/issues/2305)
* [USB Passthrough​](https://github.com/harvester/harvester/issues/1710)
* [Improving Harvester Maintenance Mode](https://github.com/harvester/harvester/issues/5069)
* [VM Snapshot Space Management](https://github.com/harvester/harvester/issues/4478)
* [Longhorn v2 data engine support](https://github.com/harvester/harvester/issues/5274) (Preview)
* [Harvester Encryption of Storage at Rest](https://github.com/harvester/harvester/issues/3129) (Experimental)
* [Harvester third-party storage support for diskless servers](https://github.com/harvester/harvester/issues/5150)

v1.5.0 (March 2025)
-----
* Dependent Component Upgrade
  * K8s - v1.31 (note: require a 1.4 patch to upgrade to v1.30)
  * Longhorn - v1.8.0
  * Rancher - TBU (v2.10.x)
  * KubeVirt - TBU (v1.3.x)
  * SLE Micro - TBU
* [Harvester VXLAN support](https://github.com/harvester/harvester/issues/2322)
* [Third-party storage for VM root disk](https://github.com/harvester/harvester/issues/1199)
* Local volume support w/ live migration (GA)
* [Harvester Support DR Volumes](https://github.com/harvester/harvester/issues/1850)
* [Harvester Complete Cluster Backup](https://github.com/harvester/harvester/issues/3263)
* [NFS for VM disks](https://github.com/harvester/harvester/issues/2272)
* Harvester Encryption of Storage at Rest (GA)
* [VM DHCP Controller GA](https://github.com/harvester/harvester/issues/5822)
* [vCluster addon GA](https://github.com/harvester/harvester/issues/5821)
* [Baremetal Container Workload Support GA](https://github.com/harvester/harvester/issues/5820)

**Waiting to review**
N/A

**Planed to re-prioritize**
N/A

v1.6.0 (July 2025)
-----
* [CIS Security Benchmark for Linux (Hypervisor) ](https://github.com/harvester/harvester/issues/2392)
* [unattend.xml support for Windows](https://github.com/harvester/harvester/issues/1836)
* Third-party CNI support
* Cisco Discovery Protocol and LLDP Support
* Improve Security by default on Rancher OS v2
* [Harvester install in maintenance mode](https://github.com/harvester/harvester/issues/4424)


v1.7.0 (November 2025)
-----
TBU

Future
------
* [[ENHANCEMENT] HA for Vcluster](https://github.com/harvester/harvester/issues/4492)
* Enhanced Integration with Rancher for massive edge deployments
* ARM Support (Experimental)
* Fleet management for Harvester (Experimental)
* [Harvester Support DR Volumes](https://github.com/harvester/harvester/issues/1850)
* Third-party Backup Solution support (note: general or specific backup integration)

Past releases
---

Harvester v1.3.1 (June 2024)
---------------------
* Dependent Component Upgrade
  * K8s v1.27
  * Longhorn v1.6.2
  * Rancher v2.8.3
  * KubeVirt v1.1.1
* Stabilization
* Security patches

Harvester v1.2.2 (May 2024)
---------------------
* Dependent Component Upgrade
  * K8s v1.26
  * Longhorn v1.5.5
  * Rancher v2.8.2
  * KubeVirt v1.1.1
* Stabilization
* Security patches

Harvester v1.3.0 (Mar 2024)
---------------------
* [vGPU support](https://github.com/harvester/harvester/issues/2764)
* [Optimize for Frequent Power-off/Power-On operating procedures](https://github.com/harvester/harvester/issues/3261)
* [Two nodes (with witness) support​](https://github.com/harvester/harvester/issues/3266)
* Manage DHCP
* ARM Support (Technical preview)
* Fleet management for Harvester (Technical preview)

Harvester v1.1.3 (Dec 2023)
---------------------

Harvester v1.2.1 (Oct 2023)
---------------------

Harvester v1.2.0 (Sept 2023)
---------------------
* [Harvester extension](https://github.com/harvester/harvester/issues/2769)
* [Out of box Rancher experience(Experimental)](https://github.com/harvester/harvester/issues/2679)
* [Reduced resource footprint](https://github.com/harvester/harvester/issues/3262)
* [Third-party storage support for non-root disk](https://github.com/harvester/harvester/issues/2405)
* [SRIOV VF passthrough support](https://github.com/harvester/harvester/issues/2763)
* [Enhanced Cloud Provider support](https://github.com/harvester/harvester/issues/2134)
* [Support configure Harvester cluster after installation](https://github.com/harvester/harvester/issues/2198)
* [Hardware error detection](https://github.com/harvester/harvester/issues/2318)

Harvester v1.1.2 (Apr 2023)
---------------------
* Create a template from VM that includes the root and data disks [[#3545](https://github.com/harvester/harvester/issues/3545)]
* Maintenance mode doesn't drain the node [[#3363](https://github.com/harvester/harvester/issues/3363)]
* Harvester node driver supports multiple NICs and multiple disks [[#3359](https://github.com/harvester/harvester/issues/3359)]
* Harvester CSI Driver supports storage tiering [[#3052](https://github.com/harvester/harvester/issues/3052)]
* terraform-provider-rancher2 to support to add additional NIC and disks [[#2733](https://github.com/harvester/harvester/issues/2733)]

Harvester v1.1.1 (Dec 2022)
---------------------
*   Stabilization

Harvester v1.1.0 (Oct 2022)
---------------------
* [Log collection and export](https://github.com/rancher/harvester/issues/577) and [Event recording](https://github.com/rancher/harvester/issues/578)
  *   Syslog forwarding of hypervisor host logs
* [PCI-E passthrough (and GPU support)](https://github.com/harvester/harvester/issues/992)
* VM Snapshot enhancement
  * [VM Snapshot/revert](https://github.com/rancher/harvester/issues/553)
  * [VM clone based on in-cluster snapshot](https://github.com/rancher/harvester/issues/569)
* [Storage Network](https://github.com/harvester/harvester/issues/1055)
* [Enhanced image management](https://github.com/harvester/harvester/issues/2319)
* [VLAN for mgmt network](https://github.com/harvester/harvester/issues/2236)
* [Support seamless migration from other virtualization platforms.](https://github.com/harvester/harvester/issues/2274)

Harvester v1.0.3 (Aug 2022)
---------------------
* [Rebase Harvester on SLE Micro for Rancher](https://github.com/harvester/harvester/issues/1933)

Harvester v1.0.2 (May 2022)
---------------------
* [Upgrade from v1.0.1](https://github.com/harvester/harvester/issues/1861)
* [Support bundle for a single node](https://github.com/harvester/harvester/issues/1864)
* [Legacy Iso for older servers](https://github.com/harvester/harvester/issues/2023)
* [Enhancement of qemu-guest-agent design](https://github.com/harvester/harvester/issues/2002)
* [WebUI adds setting of monitoring-prometheus-node-exporter](https://github.com/harvester/harvester/issues/2075)
* [Harvester NODE IP, VIP enhancement](https://github.com/harvester/harvester/issues/1683)

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
Harvester v0.3.0 (Q3 2021)
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


v1.0.0 GA (Q4 2021)
---------------------

*   Stabilization

v1.1.0
------
*   [VM Snapshot/revert](https://github.com/rancher/harvester/issues/553)
*   [VM clone based on in-cluster snapshot](https://github.com/rancher/harvester/issues/569)
*   [Log collection and export](https://github.com/rancher/harvester/issues/577)
    *   Syslog forwarding of hypervisor host logs
*   [Event recording](https://github.com/rancher/harvester/issues/578)

Future
------

*   Container and VM network integration or path to communication 
*   Built-in PXE server
*   VXLAN
*   Support local path provisioner (no VM migration/HA)
*   Differential / layered image downloaded (to reduce network traffic with large images)
* Enhanced Integration with Rancher for massive edge deployments
* Network enhancement: support telco network solutions
* Support seamless migration from other virtualization platforms.

Past releases
---
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

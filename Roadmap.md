# Harvester v0.1.0 (alpha)
### Installation
Two modes:
* Standalone installation on bare-metal.
* Install on existing Kubernetes cluster using Helm/Rancher.

### VM Lifecycle management
* VM create, start, stop, and delete operations
* cloud-init
* Admin UI 
* SSH key support
* Built-in console
    * Both VNC and serial port

### Storage
* Raw block device (backing by Longhorn)

### Networking
* VLAN
    * No IPAM
* Management network
   
### Authentication
* Local user
    * Authenticate using generated/specified username/password during installation
    * Multi-user support (admin only)

### Image management
* Built-in image repo (backing by MinIO)

# Future Releases
### Storage
* Live migration

### Networking
* VPC 

### Authentication
* RBAC
* LDAP/AD

### Image management
* HA for image store

### Maintenance
* Zero downtime upgrade

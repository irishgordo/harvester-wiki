### Using VMware ESXi
For testing purposes, users can create a Harvester cluster using VMware ESXi with the nested virtualization feature enabled, the example below uses ESXi v6.7.0: 
1. Click `Create/Register VM` and choose to create a new virtual machine
1. Choose the guest OS family to `Linux` and guest OS version to `Other Linux(64-bit)
1. Set the minimal CPU, Memory, and storage size to 4C, 8G, and 120G respectively, and ensure you have selected the hardware virtualization, IOMMU, and Performance counters options within the CPU section.
1. Click Finish and launch to start installing the Harvester to this virtual machine.

<small>Customize VM settings</small>
![](https://ibb.co/Vm4Zh6c)

## Overview

Harvester's base operating system is derived from [SUSE Linux Micro](https://www.suse.com/products/micro/).

Container images for the base OS (and headers, needed for nvidia-driver-toolkit) are built in subprojects of [isv:Rancher:Harvester:OS](https://build.opensuse.org/project/subprojects/isv:Rancher:Harvester:OS) on [OBS](https://build.opensuse.org/), and published on [registry.opensuse.org](https://registry.opensuse.org/cgi-bin/cooverview?srch_term=project%3D%5Eisv%3ARancher%3AHarvester%3AOS%3A+container%3D.*). The subprojects are named for Harvester versions, and produce two images (baseos and baseos-headers) for each version stream of Harvester, plus the "Dev" or next version.  These images are automatically rebuilt when the underlying SL Micro image or any included packages are updated.

| OBS Project/Package | Container Image |
| ------------------- | --------------- |
| [isv:Rancher:Harvester:OS:Dev/baseos](https://build.opensuse.org/package/show/isv:Rancher:Harvester:OS:Dev/baseos) | registry.opensuse.org/isv/rancher/harvester/os/dev/main/baseos:latest |
| [isv:Rancher:Harvester:OS:Dev/baseos-headers](https://build.opensuse.org/package/show/isv:Rancher:Harvester:OS:Dev/baseos-headers) | registry.opensuse.org/isv/rancher/harvester/os/dev/main/baseos-headers:latest |
| [isv:Rancher:Harvester:OS:v1.5/baseos](https://build.opensuse.org/package/show/isv:Rancher:Harvester:OS:v1.5/baseos) | registry.opensuse.org/isv/rancher/harvester/os/v1.5/main/baseos:latest |
| [isv:Rancher:Harvester:OS:v1.5/baseos-headers](https://build.opensuse.org/package/show/isv:Rancher:Harvester:OS:v1.5/baseos-headers) | registry.opensuse.org/isv/rancher/harvester/os/v1.5/main/baseos-headers:latest |
| [isv:Rancher:Harvester:OS:v1.4/baseos](https://build.opensuse.org/package/show/isv:Rancher:Harvester:OS:v1.4/baseos) | registry.opensuse.org/isv/rancher/harvester/os/v1.4/main/baseos:latest |
| [isv:Rancher:Harvester:OS:v1.4/baseos-headers](https://build.opensuse.org/package/show/isv:Rancher:Harvester:OS:v1.4/baseos-headers) | registry.opensuse.org/isv/rancher/harvester/os/v1.4/main/baseos-headers:latest |
| [isv:Rancher:Harvester:OS:v1.3/baseos](https://build.opensuse.org/package/show/isv:Rancher:Harvester:OS:v1.3/baseos) | registry.opensuse.org/isv/rancher/harvester/os/v1.3/main/baseos:latest |
| [isv:Rancher:Harvester:OS:v1.3/baseos-headers](https://build.opensuse.org/package/show/isv:Rancher:Harvester:OS:v1.3/baseos-headers) | registry.opensuse.org/isv/rancher/harvester/os/v1.3/main/baseos-headers:latest |

If we need to add additional packages to the baseos for whatever reason, we do so by updating the `zypper in [...]` line in the appropriate Dockerfile(s) on OBS, because the build environment there has access to all the underying SL Micro packages.

Where there are additional packages necessary to include in the baseos that _aren't_ available in the underying SL Micro repositories (or that are present but outdated), we have packaged those in subprojects of [isv:Rancher:Harvester:ExtraPackages](https://build.opensuse.org/project/subprojects/isv:Rancher:Harvester:ExtraPackages).

The baseos container images from OBS are always the latest and greatest for a given version stream.

On top of baseos, we have [os2](https://github.com/harvester/os2), which takes the above container images, adds additional files necessary for [Elemental](https://github.com/rancher/elemental-toolkit/) configuration, and extra software that isn't packaged as an RPM, such as `rancherd`.  Releases in the os2 repo are tagged periodically by the release captain for each Harvester release (see [[Create a Harvester release]]).  There are Github actions in place to publish os2 images to [rancher/harvester-os](https://hub.docker.com/r/rancher/harvester-os) and [rancher/nvidia-driver-toolkit](https://hub.docker.com/r/rancher/harvester-nvidia-driver-toolkit/) on Docker Hub.

Finally, [harvester-installer](https://github.com/harvester/os2) uses the os2 images from [rancher/harvester-os](https://hub.docker.com/r/rancher/harvester-os) to package everything up into the Harvester installer ISO.

Branches of harvester-installer and os2 map to harvester-os container images as follows:

| harvester-installer | os2       | rancher/harvester-os |
| ------------------- | --------- | -------------------- |
| master              | sle-micro | rancher/harvester-os:sle-micro-head |
| v1.5                | harvester-v1.5.x | rancher/harvester-os:v1.5-YYYYMMDD |
| v1.4                | harvester-v1.4.x | rancher/harvester-os:v1.4-YYYYMMDD |
| v1.3                | harvester-v1.3.x | rancher/harvester-os:v1.3-YYYYMMDD |

Specific Harvester releases map to specific tags, for example:

| harvester-installer | os2       | rancher/harvester-os |
| ------------------- | --------- | -------------------- |
| v1.5.0-rc3          | v1.5-20250324 | rancher/harvester-os:v1.5-20250324
| v1.4.2              | v1.4-20250311 | rancher/harvester-os:v1.4-20250311
| v1.3.2              | v1.3-20240904 | rancher/harvester-os:v1.3-20240904

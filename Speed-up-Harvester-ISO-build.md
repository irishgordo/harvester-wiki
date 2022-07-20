## Overview
We can build a Harvester ISO from the https://github.com/harvester/harvester-installer repository by:
```
git clone https://github.com/harvester/harvester-installer
cd harvester-installer
make
```
The build process can be summarized as:
- `scripts/build`: Build the installer binary.
- `scripts/build-bundle`:
  - This is the most time-consuming part.
  - Clone `harvester/harvester` repo to build Helm repos.
  - Extract all needed container images from Helm chart values.yaml files, pull images and save images as tarballs.
  - Download RKE2 image tarballs from Github.
- `scripts/package-harvester-repo`:
- `scripts/package-harvester-os`: package all stuff with os2 image and create an ISO.

There are some tips to speed up the build.

## Skip images pulling in `scripts/build-bundle` stage

We can first build an ISO and extract image tarballs from the ISO as a cache to speed up the build.

> **Warning**
> 
> This works only:
> - You don't modify any image tags in harvester/harvester charts and its dependency charts.
> - You don't need to bump RKE2 version or Rancher versions.

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
> - You don't modify any image tags in harvester/harvester charts and its dependency charts. In short, container images are not changed.
> - You don't need to bump RKE2 version or Rancher versions.

Steps
- Build an ISO first.
- Extract the bundle, the following script should help:
  ```
  # Modify paths if needed
  export ISO=/tmp/harvester.iso
  export BUNDLE_CACHE=/srv/www/htdocs/harvester/bundle

  xorriso -osirrox on -indev ${ISO} -extract /bundle $BUNDLE_CACHE
  rm -f $BUNDLE_CACHE/harvester/images/harvester-repo-images*
  rm -f $BUNDLE_CACHE/harvester/images-lists/harvester-repo-images*
  ```
- Edit `harvester-installer/Dockerfile.dapper`, bind-mount the bundle cache directory to `/bundle`:
  ```
  ENV DAPPER_RUN_ARGS "-v /run/containerd/containerd.sock:/run/containerd/containerd.sock -v harvester-installer-go:/root/go -v harvester-installer-cache:/root/.cache -v /srv/www/htdocs/harvester/bundle:/bundle"
  ```
- Edit `harvester-installer/scripts/build-bundle`
  ```
  if [ -n "$HARVESTER_INSTALLER_OFFLINE_BUILD" -a -e /bundle ]; then
    cp -rf /bundle/* ${BUNDLE_DIR}/
    pushd ${harvester_path}
    git checkout -- ./deploy/charts
    popd
    exit 0
  fi
  ```
- Build an ISO
  ```
  HARVESTER_INSTALLER_OFFLINE_BUILD=y make
  ```

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

## Skip images pulling in `build-bundle` stage

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

## Use local RKE2 images files
**NOTE**: Don't use this with the image tarball cache tip.

- Check the current RKE2 version
  ```
  # in harvester-installer
  $ cat scripts/version-rke2
  RKE2_VERSION="v1.22.12-rc3+rke2r1"
  ```
- Mirror RKE2 image tarballs to a place. The script should help:
  ```
  $ cat get-rke2.sh
  #!/bin/bash -eux

  VERSION=$1

  mkdir -p $VERSION

  cd $VERSION

  wget https://github.com/rancher/rke2/releases/download/$VERSION/rke2-images.linux-amd64.tar.zst
  wget https://github.com/rancher/rke2/releases/download/$VERSION/rke2-images.linux-amd64.txt
  wget https://github.com/rancher/rke2/releases/download/$VERSION/rke2-images-multus.linux-amd64.txt
  wget https://github.com/rancher/rke2/releases/download/$VERSION/rke2-images-harvester.linux-amd64.tar.zst
  wget https://github.com/rancher/rke2/releases/download/$VERSION/rke2-images-harvester.linux-amd64.txt

  # verify
  wget https://github.com/rancher/rke2/releases/download/$VERSION/sha256sum-amd64.txt
  sha256sum -c --ignore-missing sha256sum-amd64.txt

  $ ./get-rke2.sh v1.22.12-rc3+rke2r1
  # this saves to folder `v1.22.12-rc3+rke2r1`
  ```
- Host the files in a webserver and build with it, e.g.:
  ```
  RKE2_IMAGE_REPO=http://192.168.2.106/harvester/rke2/ make
  ```

## Use zstd compression for squash image
Edit `package/harvester-os/iso.yaml`.

```
diff --git a/package/harvester-os/iso.yaml b/package/harvester-os/iso.yaml
index 5ccc95d..66137d6 100644
--- a/package/harvester-os/iso.yaml
+++ b/package/harvester-os/iso.yaml
@@ -21,4 +21,4 @@ luet:
         - quay.io/costoolkit/releases-green
       type: docker
 squashfs_options:
-  compression: xz
+  compression: zstd
```

 
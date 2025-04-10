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

Finally, [harvester-installer](https://github.com/harvester/harvester-installer) uses the os2 images from [rancher/harvester-os](https://hub.docker.com/r/rancher/harvester-os) to package everything up into the Harvester installer ISO.

Branches of harvester-installer and os2 map to harvester-os container images as follows:

| harvester-installer | os2       | rancher/harvester-os | Image Published |
| ------------------- | --------- | -------------------- | --------- |
| master              | sle-micro | rancher/harvester-os:sle-micro-head | daily (automatic)
| v1.5                | harvester-v1.5.x | rancher/harvester-os:v1.5-YYYYMMDD | when tagged |
| v1.4                | harvester-v1.4.x | rancher/harvester-os:v1.4-YYYYMMDD | when tagged |
| v1.3                | harvester-v1.3.x | rancher/harvester-os:v1.3-YYYYMMDD | when tagged |

Specific Harvester releases map to specific tags, for example:

| harvester-installer | os2       | rancher/harvester-os |
| ------------------- | --------- | -------------------- |
| v1.5.0-rc3          | v1.5-20250324 | rancher/harvester-os:v1.5-20250324
| v1.4.2              | v1.4-20250311 | rancher/harvester-os:v1.4-20250311
| v1.3.2              | v1.3-20240904 | rancher/harvester-os:v1.3-20240904

Harvester's daily ISO builds from the master branch will always use the latest rancher/harvester-os:sle-micro-head image.

## Where to Make Changes

From time to time, we need to make changes to the base operating system.  Where to make the change depends on how broad or generic it is:

- If we need to add or remove packages from the OS, we do it in baseos and/or baseos-headers in [isv:Rancher:Harvester:OS](https://build.opensuse.org/project/subprojects/isv:Rancher:Harvester:OS) on [OBS](https://build.opensuse.org/).
- If we need to tweak configuration, or if for some other reason we have packages that can't be handled on OBS, we do it in either [os2](https://github.com/harvester/os2) or [harvester-installer](https://github.com/harvester/harvester-installer).

Here's some examples:

- Extra packages are required, as in [[BUG] helm missing from Harvester install](https://github.com/harvester/harvester/issues/4518) and [[FEATURE] Update nvidia-driver-toolkit build to support FIPS-enabled OS](https://github.com/harvester/harvester/issues/8006).  These changes were made in projects on OBS.
- We need to tweak some OS configuration files as in [fix: ensure machine-id, initiatorname.iscsi and host{id,nqn} are unique](https://github.com/harvester/os2/pull/190), or we need to update some included software that isn't packaged as an RPM as in [feat: bump Rancherd to v0.5.0-rc1](https://github.com/harvester/os2/pull/192).  These changes were made in os2.
- We need to do something strange and weirdly specific as in [change order of interface startup](https://github.com/harvester/harvester-installer/pull/868).  This change was made in harvester-installer.

In all cases, changes should be made first to the dev or master project or branch, then backported or submitted to whatever release projects or branches are appropriate.

## How to Cheat When You Need to Change Something for Development Purposes

Given the way the build process is structured, it can be tricky to make experimental changes to the base OS for development purposes.  For example, to add a new package:

1. Branch baseos on OBS and make whatever changes are required. Images from your branch will be published to registry.opensuse.org/home/YOUR_USERNAME/branches/isv/rancher/harvester/os/dev/main/baseos:latest.
2. Fork os2 and update its [Dockerfile](https://github.com/harvester/os2/blob/65dfa111ba82c2fdb98d9d024a4330b535be2c3e/Dockerfile#L3) to build based on your image above, then build and push your os2 image your personal account on Dockerhub or a local registry.
3. Fork harvester-installer and update BASE_OS_IMAGE in [package-harvester-os](https://github.com/harvester/harvester-installer/blob/b5a284fccd5720d3b4bf81368902e07c595cb524/scripts/package-harvester-os#L26) to refer to your custom os2 image above.
4. Run `make` to build an ISO.

Instead of doing all that, it's easier to cheat and just make all required changes directly in your fork of harvester-installer.  Once you've validated that your changes work, they can be submitted individually to wherever they really need to go, be it baseos on OBS, or os2 or harvester-installer on Github.

The problem with cheating like this is that the baseos container image doesn't have any software repositories configured.  This is fine when building on OBS, which automatically makes the correct repositories available in the build environment, but it doesn't work for local builds.  To work around this, we can add the publically available openSUSE repositories.  These are not 100% identical to those used when baseos is builton OBS, but they should generally be close enough for quick develompent or testing work.  Here's what you need to do in harvester-installer if you want to try adding some extra packages:

```diff
diff --git a/package/harvester-os/Dockerfile b/package/harvester-os/Dockerfile
index ba37af2..9e480bb 100644
--- a/package/harvester-os/Dockerfile
+++ b/package/harvester-os/Dockerfile
@@ -1,6 +1,14 @@
 ARG BASE_OS_IMAGE
 FROM ${BASE_OS_IMAGE}
 
+RUN \
+    zypper addrepo http://download.opensuse.org/distribution/leap/15.5/repo/oss/ oss && \
+    zypper addrepo http://download.opensuse.org/update/leap/15.5/oss update-oss && \
+    zypper addrepo http://download.opensuse.org/update/leap/15.5/backports update-backports && \
+    zypper addrepo http://download.opensuse.org/update/leap/15.5/sle update-sle && \
+    zypper --gpg-auto-import-keys refresh && \
+    zypper install -y INSERT EXTRA PACKAGES TO INSTALL HERE
+
 ```

Then, run `make` to build your ISO image.

_NOTE: This is ONLY appropriate for local development or testing purposes.  DO NOT INCLUDE `zypper addrepo` lines when submitting your final changes back to baseos on OBS.  Thank you :-)_
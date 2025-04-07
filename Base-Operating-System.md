baseos & baseos-headers are built on OBS (packages installed here)
used for os2, which further customizes the image (adds some files, removes some things), then builds rancher/harvester-os and rancher/harvester-nvidia-driver-toolkit
rancher/harvester-os is used by harvester-installer when building the installable ISO

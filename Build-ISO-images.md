We can build harvester ISO images in [harvester](https://github.com/harvester/harvester) and [harvester-installer](https://github.com/harvester/harvester-installer) repositories. Images will be under `dist/artifacts` path.
1. If you only modify the harvester repo, you can run the following commands in the harvester repo:
  
+  Using container registry:

```bash
REGISTRY="replace to your container registry, such as harbor.futuretea.me"
PROJECT="replace to your project name in the container registry, such as rancher"
docker login ${REGISTRY}
cp ~/.docker . -r
export REPO=${REGISTRY}/${PROJECT}
export PUSH=true
make
make build-iso
```

+  Using dockerhub:
```bash
DOCKERHUB_USERNAME="replace to your username of dockerhub, such as futuretea"
docker login
cp ~/.docker . -r
export REPO=${DOCKERHUB_USERNAME}
export PUSH=true
make
make build-iso
```

+  Using local docker cache via ENV **USE_LOCAL_IMAGES** (do not rely on any of the above)

```bash
(1) git clone ...
(2) git checkout -b brancher-name ... # add code and commit
(3) make && make package && make package-webhook && make package-upgrade
#3 images (harvester, harvester-webhook,harvester-upgrade) taged as `brancher-name-head` are available on local docker cache
(4) export USE_LOCAL_IMAGES=brancher-name-head
(5) make build-iso
```
```
there will be such log:
[ImageCache] docker.io/rancher/harvester-upgrade:fix6486-head meets the specified tag from USE_LOCAL_IMAGES.
[ImageCache] docker.io/rancher/harvester-upgrade:fix6486-head exists.
..
[ImageCache] docker.io/rancher/harvester-webhook:fix6486-head meets the specified tag from USE_LOCAL_IMAGES.
[ImageCache] docker.io/rancher/harvester-webhook:fix6486-head exists.

[ImageCache] docker.io/rancher/harvester:fix6486-head meets the specified tag from USE_LOCAL_IMAGES.
[ImageCache] docker.io/rancher/harvester:fix6486-head exists.

The 3 images built from your branch are used for the final Harvester ISO.

```




2. If you only modify the harvester-installer repo, you can run `make` in the harvester-installer repo.
3. If you modify both repositories...

+ ...and are using the master branch since https://github.com/harvester/harvester-installer/pull/763 (2024-07-10), you can set the LOCAL_HARVESTER_SRC environment variable then run `make` in the harvester-installer repo:

```bash
export LOCAL_HARVESTER_SRC=/path/to/local/harvester/repo
make
```

+ For other branches (v1.3 and earlier) update this line like the following and run `make` in the harvester-installer repo.

```
ENV DAPPER_RUN_ARGS "-v /var/run/docker/containerd/containerd.sock:/run/containerd/containerd.sock -v /path/to/local/harvester/repo:/go/src/github.com/harvester/harvester"
```
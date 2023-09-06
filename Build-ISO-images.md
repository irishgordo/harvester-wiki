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

2. If you only modify the harvester-installer repo, you can run `make build` in the harvester-installer repo.
3. If you modify both repositories, you can update this line like the following and run `make` in the harvester-installer repo.

```
ENV DAPPER_RUN_ARGS="-v /var/run/docker/containerd/containerd.sock:/run/containerd/containerd.sock -v /path/to/local/harvester/repo:/go/src/github.com/harvester/harvester" make
```
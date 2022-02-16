We can build harvester ISO images in [harvester](https://github.com/harvester/harvester) and [harvester-installer](https://github.com/harvester/harvester-installer) repositories. Images will be under `dist/artifacts` path.
* If you only modify the harvester repo, you can run `make build-iso` in the harvester repo.
* If you only modify the harvester-installer repo, you can run `make build` in the harvester-installer repo.
* If you modify both repositories, you can update this line like the following and run `make build` in the harvester-installer repo.

```
ENV DAPPER_RUN_ARGS "-v /var/run/docker/containerd/containerd.sock:/run/containerd/containerd.sock -v /path/to/local/harvester/repo:/go/src/github.com/harvester/harvester"
```
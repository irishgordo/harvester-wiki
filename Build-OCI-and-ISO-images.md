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
        (1) git clone ... # clone the repo
        (2) git checkout -b brancher-name ... # add code and commit
        (3) make # it calls scripts/default (make package && make package-webhook && make package-upgrade)
        #3 images (harvester, harvester-webhook,harvester-upgrade) taged as `brancher-name-head` are available on local docker cache
        (4) export USE_LOCAL_IMAGES=brancher-name-head
        (5) make build-iso
        ```

        There will be such log:
        ```
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

### Troubleshooting
* BuildKit errors in spite of having it installed:
    ```
    $ make
    ...
    ...
    failed to fetch metadata: fork/exec /go/src/github.com/harvester/harvester/.docker/cli-plugins/docker-buildx: no such file or directory

    ERROR: BuildKit is enabled but the buildx component is missing or broken.
        Install the buildx component to build images with BuildKit:
        https://docs.docker.com/go/buildx/
    FATA[0113] exit status 1
    make: *** [Makefile:11: default] Error 1
    ```
    Try building without BuildKit if you face this error. To do so, run commands above after removing the `.docker` directory from your git repository's root. It was copied using the command `cp ~/.docker . -r`. Another option is to move it into a separate backup directory.
    ```sh
    # option 1: remove the .docker directory
    $ rm -rf /path/to/harvester/git/repository/.docker

    # option 2: move it into a separate directory for backup
    $ mv /path/to/harvester/git/repository/.docker /path/to/harvester/git/repository/docker.bkp
    ```
## Introduction 

Firstly talk about how we built with Drone CI.

We used different platforms to build the binary and image like this:

```yaml
kind: pipeline
name: amd64

platform:
  os: linux
  arch: amd64
---
kind: pipeline
name: arm64

platform:
  os: linux
  arch: arm64
```

But, in Github Action, we can just use `docker/build-push-action` with `platforms: linux/amd64,linux/arm64` to build image like this:

```yaml
name: Docker Build
uses: docker/build-push-action@v5
with:
  context: .
  platforms: linux/amd64,linux/arm64
  file: {where your Dockerfile is}
  push: true # push to Docker Hub after successful building
  tags: {repo_name}:{repo_tag}
```

That means we used to build different binaries in different pipelines before, but now we can build different binaries at the same time with `make ci`. 

The steps would be:

1. Run `make ci` to generate amd64 and arm64 binaries in our Github runner which is linux/amd64.
2. Build image with previous binary based on `platforms: linux/amd64,linux/arm64`.

So, we should remove `ARCH` in our scripts/x files, then make sure we generate linux/amd64,linux/arm64 binaries when running `make ci`. After that, let Dockerfile pick correct binary to build image.

## Examples

Let's jump to examples.

- [In this support-bundle-kit PR](https://github.com/rancher/support-bundle-kit/pull/107/files), we removed the `$ARCH` and generate two binaries. But, we built two platform images in scripts/package which is unnecessary because `docker/build-push-action@v5` already did that for us. In this PR. In this case, it slows down local development building time.

- [In this support-bundle-kit PR](https://github.com/rancher/support-bundle-kit/pull/110/files), we only kept a simple command to build image based on your local environment. The `buildx` will pick correct binary for you to build image, so we don't need to specify which platform we need. We could get the [default platform value](https://docs.docker.com/reference/cli/docker/buildx/build/#platform) with `TARGETPLATFORM` in Dockerfile when using `buildx`. It fixes the building time issue.

Although there might be slightly difference in all projects, but I think this should be helpful to move to Github Action.

## Testing

Gaurav recommends https://github.com/nektos/act, it can run Github Action in local. 

Example commands: `act pull_request --secret-file ~/secret.txt --container-architecture linux/amd64`

```txt
# secret.txt
DOCKER_USERNAME=foo
```
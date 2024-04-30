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

That means we used to build different binaries in different platforms before, but now we can only build different binaries at the same time in a single runner by using `make ci`. 

The steps would be:

1. Run `make ci` to generate amd64 and arm64 binaries in our Github runner which is linux/amd64.
2. Build image with previous binary based on `platforms: linux/amd64,linux/arm64`.

So, we should remove `ARCH` in our scripts/x files, then make sure we generate linux/amd64,linux/arm64 binaries when running `make ci`. After that, let Dockerfile pick correct binary to build image. 

There are few things need to notice:

- Some repos might have command that copy binaries to artifacts folders, but it's not used in the end. Remember to remove this.
- No need to use `VERSION` with build-args in normal case. Remember to remove this if you don't need that.

## Examples

Let's jump to examples.

- [In this support-bundle-kit PR](https://github.com/rancher/support-bundle-kit/pull/107/files), we removed the `$ARCH` and generate two binaries. But, we built two platform images in scripts/package which is unnecessary because `docker/build-push-action@v5` already did that for us. In this PR. In this case, it slows down local development building time.

- [In this support-bundle-kit PR](https://github.com/rancher/support-bundle-kit/pull/110/files), we only kept a simple command to build image based on your local environment. The `buildx` will pick correct binary for you to build image, so we don't need to specify which platform we need. We could get the [default platform value](https://docs.docker.com/reference/cli/docker/buildx/build/#platform) with `TARGETPLATFORM` in Dockerfile when using `buildx`. It fixes the local building time issue.

> local build command: `./scripts/build` && `./scripts/package`

Although there might be slightly difference in all projects, but I think this should be helpful to move to Github Action.

## Testing

Gaurav recommends https://github.com/nektos/act, it can run Github Action in local. 

Example commands

- Pull Request: `act pull_request --secret-file ~/secret.txt --container-architecture linux/amd64`
- Push: `act push --secret-file ~/secret.txt --container-architecture linux/amd64`

```txt
# secret.txt
DOCKER_USERNAME=foo
```

### Notes

When you test locally, remember to: 
1. Remove `rancher-eio/read-vault-secrets@main` step
2. Change `env.DOCKER_USERNAME`/`env.DOCKER_PASSWORD` to `secrets.DOCKER_USERNAME`/`secrets.DOCKER_PASSWORD`.

## Others

We could also use matrix to distribute building to different runners to build binary and image. [In this example PR](https://github.com/Yu-Jack/support-bundle-kit/pull/6), it's not turning runner to specified matrix.platform, [matrix.platform is just a for-loop concept](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs). If we need to build more platform in the future, we could refer [distribute-build-across-multiple-runners](https://docs.docker.com/build/ci/github-actions/multi-platform/#distribute-build-across-multiple-runners).
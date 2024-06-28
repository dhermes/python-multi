## [`python-multi`][1] Dockerfile

This is a single purpose repository intended to provide a Dockerfile for an
image with multiple Python versions installed.

The Dockerfile uses `pyenv` to install the included Python versions.

## Short Description

The "Short Description" on Docker Hub should read:

> Multiple Python versions installed into Ubuntu LTS (22.04 as of February 2024)

This is provided here as a back-up (i.e. in case the short
description is overwritten).

## Commands

This is a collection of useful `docker` commands relevant to
this directory. Some of them are just generic `docker` commands
for making sure the local system is in a good state (useful
for those who don't use `docker` on a regular basis).

### Combine multiple architectures

Via [Multi-arch build and images, the simple way][2]:

```bash
docker build \
  --file src/Dockerfile \
  --platform linux/amd64 \
  --tag dhermes/python-multi:1.YYYYMMDD.N-amd64 \
  .

docker build \
  --file src/Dockerfile \
  --platform linux/arm64 \
  --tag dhermes/python-multi:1.YYYYMMDD.N-arm64 \
  .

# NOTE: Tags must be pushed for `docker manifest create` to find them, they
#       can later be removed.
docker push dhermes/python-multi:1.YYYYMMDD.N-amd64
docker push dhermes/python-multi:1.YYYYMMDD.N-arm64

############################################################

docker manifest create \
  dhermes/python-multi:1.YYYYMMDD.N \
  --amend dhermes/python-multi:1.YYYYMMDD.N-amd64 \
  --amend dhermes/python-multi:1.YYYYMMDD.N-arm64

docker manifest push dhermes/python-multi:1.YYYYMMDD.N

############################################################

docker manifest create \
  dhermes/python-multi:latest \
  --amend dhermes/python-multi:1.YYYYMMDD.N-amd64 \
  --amend dhermes/python-multi:1.YYYYMMDD.N-arm64

docker manifest push dhermes/python-multi:latest
```

### Specific

```
$ docker build \
>   --file src/Dockerfile \
>   --tag dhermes/python-multi:latest \
>   .
$ docker run \
>   --rm \
>   --tty \
>   --interactive \
>   --volume $(git rev-parse --show-toplevel):/var/code/ \
>   dhermes/python-multi:latest \
>   /bin/bash
```

### Generic

```
$ docker system prune  # Clean-up
$ docker image  prune  # Clean-up
$ docker container ls  # Running containers
$ docker images        # List installed/cached images
$ docker image rm ${IMAGE_ID}
$ docker image rm ${REPOSITORY}:${TAG}
```

[1]: https://hub.docker.com/r/dhermes/python-multi/
[2]: https://www.docker.com/blog/multi-arch-build-and-images-the-simple-way/

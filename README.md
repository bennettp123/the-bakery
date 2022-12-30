# the-bakery

A sample repo for building multi-platform images from `docker-compose.yaml`
with caching, using github actions.

## Overview

1. Images are defined in [`docker-compose.yaml`](https://github.com/bennettp123/the-bakery/blob/main/docker-compose.yaml)
2. Github action in [`bake.yaml`](https://github.com/bennettp123/the-bakery/blob/main/.github/workflows/bake.yaml)

### `docker-compose.yaml`

* three targets: `sample-one`, `sample-two`, `sample-three`
* normally, they can would be built using `docker-compose build` or `docker buildx bake`

### `bake.yaml`

* Gets the list of targets using `docker buildx bake --print`
* Generate `bake-settings`, with cache-from and target platform.
* Creates a GHA matrix from the targets
* Uses [`docker/metadata-action`](https://github.com/docker/metadata-action) to generate tags and labels. By default, this action writes its output to a target named `docker-metadata-action`; the [example](https://github.com/docker/metadata-action#bake-definition) inherits this target. However, `buildx bake` does not support `inherits` when building from docker-compose.yaml. To work around this, override the bake target to match the target we are building. 
* Uses [`docker/bake-action`](https://github.com/docker/bake-action) to build the target. We scope it to a single target using the GHA matrix `${{ matrix.target }}`.

Possible improvements:

* The settings in `bake-settings` could be part of the docker-compose.yaml&mdash; just use `x-bake` extensions.
* No need for multiple build nodes&mdash;we could just use a single node (no need for the matrix)&mdash;though for large builds this may be significantly slower due to reduced parallelism.

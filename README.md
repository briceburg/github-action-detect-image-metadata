# github-action-detect-image-metadata
An action to determine docker image tags and labels based on GitHub events and refs.

## Usage

Pairs well with [github-action-build-dockerfile](https://github.com/briceburg/github-action-build-dockerfile) to build + publish an image based on detected tags and labels. 

```yaml
jobs:
  publish-dockerfile:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
      with:
        ref: ${{ github.event.pull_request.head.sha || '' }}

    - name: Gather Image Labels and Tags
      id: meta
      uses: briceburg/github-action-detect-image-metadata

    - name: Log in to the Container registry
      uses: docker/login-action
      with:
        registry: ${{ steps.meta.outputs.registry }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}

    - name: Build and Publish Docker Image
      uses: briceburg/github-action-build-dockerfile
      with:
        context: .
        dockerfile-path: src/Dockerfile
        image-labels: ${{ steps.meta.outputs.labels }}
        images: ${{ steps.meta.outputs.images }}
```

## Inputs

| name | description | required | default |
| --- | --- | --- | --- |
| `image-name` | <p>Image name being built, without tag. e.g. acme/foo</p> | `false` | `${{ github.repository }}` |
| `image-registry` | <p>Image registry, e.g. 'docker.io'.</p> | `false` | `ghcr.io` |


## Outputs

| name | description |
| --- | --- |
| `extra-tags` | <p>Comma separated list of additional tags (in addition to version tag).</p> |
| `image` | <p>Primary (first) image</p> |
| `images` | <p>Newline separated list of all images.</p> |
| `labels` | <p>Newline separated list of labels.</p> |
| `name` | <p>Image Name</p> |
| `registry` | <p>Image Registry</p> |
| `version` | <p>Image Version (primary image tag)</p> |

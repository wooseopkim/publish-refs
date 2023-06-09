# Publish Refs Workflow

## Summary

This is a GitHub Actions workflow to publish images built from the given refs of a GitHub repository.

## User Guide

### Inputs

|NAME|REQUIRED|EXAMPLE|
|-|-|-|
|refs|true|`'["1.0.0", "1.1.0", "1.2.0"]'`|
|github-repository|true|`original/repository`|
|dockerhub-repository|true|`your/repository`|
|pre-build-script|false|`echo starting`|
|post-build-script|false|`echo done`|
|push-image|false (default: `false`)|`true`|
|platforms|false|`linux/arm64`|
|docker-context|false (default: `.`)|`docker`|
|dockerfile|false (default: `Dockerfile`)|`docker/Dockerfile`|

### Example

```yaml
jobs:
  build:
    name: Build `dani-garcia/vaultwarden`
    uses: wooseopkim/publish-refs/workflow.yml@v1
    with:
      refs: '["1.26.0", "1.16.2"]'
      github-repository: dani-garcia/vaultwarden
      # IMAGE, TAG, CHECKOUT_PATH are injected
      pre-build-script: |
        ls -R $CHECKOUT_PATH
      # You can access files in your caller repository because this workflow `checkout`s for you.
      post-build-script: |
        ./scripts/do_something $IMAGE $TAG
      push-image: false
      platforms: linux/amd64
      dockerfile: docker/amd64/Dockerfile.alpine
```

## Motivation

Being official, Docker images are often outdated or missing support for certain platforms. For instance, [`restic/restic`](https://hub.docker.com/r/restic/restic) lacks `linux/arm64` images even though [they release pre-built binaries for that platform](https://github.com/restic/restic/releases). This workflow helps you build and publish unofficial Docker images without having to maintain your fork manually, but you could publish your own code if you want.

## Limitations and caution

This action is made for personal use and functions are limited. For example, you can only publish images to Docker Hub, not to other registries such as GitHub Container Registry. PRs are welcome though.

## See Also

This workflow is intended to accept outputs from [`unpublished-tags`](https://github.com/marketplace/actions/unpublished-tags) action as `refs` input.

To better understand how the workflow works, see [`./workflow.yml`](./workflow.yml) and refer to actions this workflow calls.

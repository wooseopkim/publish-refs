# Publish Tags Workflow

## Summary

This is a GitHub Actions workflow to publish images built from the given tags of a GitHub repository.

## User Guide

### Inputs

|NAME|REQUIRED|EXAMPLE|
|-|-|-|
|tags|true|`'["1.0.0", "1.1.0", "1.2.0"]'`|
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
  # When using custom scripts, you may want to upload artifacts.
  upload-artifacts:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Tar scripts
        run: |
          tar -cvf /tmp/artifacts.tar ./scripts/*
      - name: Upload scripts
        uses: actions/upload-artifact@v3
        with:
          name: wooseopkim_publish-tags_artifacts
          path: /tmp/artifacts.tar
  build:
    name: Build `dani-garcia/vaultwarden`
    uses: wooseopkim/publish-tags/workflow.yml@v1
    with:
      tags: '["1.26.0", "1.16.2"]'
      github-repository: dani-garcia/vaultwarden
      # Artifacts are available under the directory `~/artifacts`.
      post-build-script: |
        ~/artifacts/scripts/do_something
      push-image: false
      platforms: linux/amd64
      dockerfile: docker/amd64/Dockerfile.alpine
```

## Motivation

Being official, Docker images are often outdated or missing support for certain platforms. For instance, [`restic/restic`](https://hub.docker.com/r/restic/restic) lacks `linux/arm64` images even though [they release pre-built binaries for that platform](https://github.com/restic/restic/releases). This workflow helps you build and publish unofficial Docker images without having to maintain your fork manually, but you could publish your own code if you want.

## Limitations and caution

This action is made for personal use and functions are limited. For example, you can only publish images to Docker Hub, not to other registries such as GitHub Container Registry. PRs are welcome though.

## See Also

This workflow is intended to accept outputs from [`unpublished-tags`](https://github.com/marketplace/actions/unpublished-tags) action as `tags` input.

To better understand how the workflow works, see [`./workflow.yml`](./workflow.yml) and refer to actions this workflow calls.

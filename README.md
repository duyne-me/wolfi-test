# wolfi-test

## Overview
Container image containing with based on `WolfiOS` and built using the
[apko-publish](https://github.com/chainguard-images/actions/tree/main/apko-publish)
GitHub Action.

## Test via Podman
The image can be tested with [Podman](https://podman.io/) and will output the version:
```
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-renovate-nodejs-bash-git-flux:latest --version
```

Test wolfi-apko-with-bash:
```
podman run \
    --rm \
    --pull=always \
    ghcr.io/kastl-ars/wolfi-apko-with-bash:latest version
```

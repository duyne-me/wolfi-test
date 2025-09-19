# wolfi-test

## Overview
Container image containing with based on `WolfiOS` and built using the
[apko-publish](https://github.com/chainguard-images/actions/tree/main/apko-publish)
GitHub Action.

## Test via Podman
Install `Podman`
```sh
sudo apt update
sudo apt install -y podman
```
The image can be tested with [Podman](https://podman.io/) and will output the version:

```sh
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-renovate-nodejs-bash-git-flux:latest --version

# change --entrypoint=/usr/local/bin/flux
```

Test wolfi-apko-with-bash:
```sh
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-apko-with-bash:latest version
```

Test image build with apko and malange:
```sh
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-melange:latest 
```

podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-bash-aws-cli:latest

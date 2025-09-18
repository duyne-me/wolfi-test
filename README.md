# wolfi-test

## Overview
Container image containing [renovate](https://github.com/renovatebot/renovate).
Based on WolfiOS and built using the
[apko-publish](https://github.com/chainguard-images/actions/tree/main/apko-publish)
GitHub Action.

The image is inspired by the [Chainguard node
image](https://images.chainguard.dev/directory/image/node/overview), i.e. I
adapted the
[apko.yaml](https://github.com/chainguard-images/images/blob/main/images/node/config/template.apko.yaml)
file used to build that image and added `bash`, `git`, `flux` and `renovate`.

## Test via Podman
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-renovate-nodejs-bash-git-flux:latest --version
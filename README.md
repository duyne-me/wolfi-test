# wolfi-test

## Overview
Container image based on `WolfiOS` and built using the [apko-publish](https://github.com/chainguard-images/actions/tree/main/apko-publish) GitHub Action.

## Test via Podman

### Install `Podman`
To install Podman on your system, run the following commands:

```sh
sudo apt update
sudo apt install -y podman
```
The image can be tested with [Podman](https://podman.io/) and will output the version:

* Test `wolfi-renovate-nodejs-bash-git-flux`
```sh
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-renovate-nodejs-bash-git-flux:latest --version

# change --entrypoint=/usr/local/bin/flux
```

* Test `wolfi-apko-with-bash`:
```sh
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-apko-with-bash:latest version
```

* Test image build with `apko` and `malange`:
```sh
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-melange:latest 
```
* Test `wolfi-bash-aws-cli`
```
podman run \
    --rm \
    --pull=always \
    ghcr.io/duyne-me/wolfi-bash-aws-cli:latest
```

## Creating a Melange Key

To sign your images with Melange, you need to create a Melange key. Here's how to do it:

You can install Melange from Homebrew:
```sh
brew install melange
```
You can also install Melange from source:
```sh
go install chainguard.dev/melange@latest
```
To use the examples:
```sh
docker run --privileged -v "$PWD":/work cgr.dev/chainguard/melange build examples/gnu-hello.yaml
```

If you want to sign your `apks`, create a signing key with the `melange keygen` command:

```sh
melange keygen
generating keypair with a 4096 bit prime, please wait...
wrote private key to melange.rsa
wrote public key to melange.rsa.pub
```

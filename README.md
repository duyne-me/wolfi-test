# Wolfi Container Images

> Minimal, secure container images built with WolfiOS, Apko, and Melange

[![Build Images](https://github.com/duyhenryer/wolfi-test/actions/workflows/build-images.yml/badge.svg)](https://github.com/duyhenryer/wolfi-test/actions/workflows/build-images.yml)
[![Build Package](https://github.com/duyhenryer/wolfi-test/actions/workflows/build_package.yml/badge.svg)](https://github.com/duyhenryer/wolfi-test/actions/workflows/build_package.yml)

## Overview

This repository contains minimal, secure container images built using [WolfiOS](https://github.com/wolfi-dev/os), [Apko](https://github.com/chainguard-dev/apko), and [Melange](https://github.com/chainguard-dev/melange). All images are optimized for size, security, and contain only necessary dependencies.

### Technology Stack

- **WolfiOS**: Minimal, secure Linux distribution designed for containers
- **Apko**: Certificate-transparency-based image builds without Dockerfiles
- **Melange**: Build system for creating APK packages

> 📚 Learn more about Apko and Melange in [docs.md](docs.md)

## Project Structure

```
wolfi-test/
├── .github/workflows/     # CI/CD pipelines
│   ├── build-images.yml   # Build all container images
│   └── build-package.yml  # Build custom APK packages
├── images/                # Container image definitions
│   ├── image-renovate.yaml
│   ├── image-apko.yaml
│   ├── image-docker-dind.yaml
│   ├── image-aws-cli.yaml
│   ├── image-kubectl.yaml
│   ├── image-k8s-tools.yaml
│   └── image-flyway.yaml
├── packages/              # APK package definitions
│   ├── package-apkrane.yaml
│   ├── package-kubeconform.yaml
│   ├── package-flyway.yaml
│   └── melange-image.yaml
├── docs.md               # Apko & Melange concepts
├── melange.rsa.pub       # Public signing key
└── README.md
```

## Available Images

| Image | Purpose | Base Packages | Registry |
|-------|---------|---------------|----------|
| **Renovate** | Renovate bot with Node.js, Git, Flux CLI | bash, flux, git, renovate | `ghcr.io/duyhenryer/renovate:latest` |
| **Apko** | Apko tool with Bash utilities | apko, bash, coreutils, grep | `ghcr.io/duyhenryer/apko:latest` |
| **Docker-in-Docker** | Docker-in-Docker with Kubernetes tools | docker, kubectl, kind | `ghcr.io/duyhenryer/docker-dind:latest` |
| **AWS CLI** | AWS CLI with common utilities | aws-cli, bash, vim | `ghcr.io/duyhenryer/aws-cli:latest` |
| **kubectl** | Kubernetes CLI tool | kubectl, bash | `ghcr.io/duyhenryer/kubectl:latest` |
| **K8s Tools** | Kubernetes utilities (yq, kustomize, kubeconform) | yq, kustomize, kubeconform, bash | `ghcr.io/duyhenryer/k8s-tools:latest` |
| **Flyway** | Database migration tool with OpenJDK 17 | bash, openjdk-17-jre, flyway | `ghcr.io/duyhenryer/flyway:latest` |

## Testing Images

### Prerequisites

Install [Podman](https://podman.io/) to test the images:

```bash
sudo apt update
sudo apt install -y podman
```

### Test Renovate Image

```bash
# Test with default entrypoint
podman run --rm --pull=always \
    ghcr.io/duyhenryer/renovate:latest --version

# Test with Flux CLI
podman run --rm --pull=always \
    --entrypoint=/usr/local/bin/flux \
    ghcr.io/duyhenryer/renovate:latest version
```

### Test Apko

```bash
podman run --rm --pull=always \
    ghcr.io/duyhenryer/apko:latest version
```

### Test Docker-in-Docker

```bash
podman run --rm --pull=always \
    --privileged \
    ghcr.io/duyhenryer/docker-dind:latest
```

### Test AWS CLI

```bash
podman run --rm --pull=always \
    ghcr.io/duyhenryer/aws-cli:latest --version
```

### Test kubectl

```bash
podman run --rm --pull=always \
    ghcr.io/duyhenryer/kubectl:latest version
```

### Test K8s Tools

```bash
# Test yq
podman run --rm --pull=always \
    ghcr.io/duyhenryer/k8s-tools:latest --version

# Test kustomize
podman run --rm --pull=always \
    --entrypoint=/usr/bin/kustomize \
    ghcr.io/duyhenryer/k8s-tools:latest version

# Test kubeconform
podman run --rm --pull=always \
    --entrypoint=/usr/bin/kubeconform \
    ghcr.io/duyhenryer/k8s-tools:latest --version
```

### Test Flyway

```bash
# Test Flyway version
podman run --rm --pull=always \
    ghcr.io/duyhenryer/flyway:latest --version

# Test with database connection (example with PostgreSQL)
# Use environment variables for sensitive data
podman run --rm --pull=always \
    -v $(pwd)/sql:/opt/flyway/sql \
    -e FLYWAY_URL=jdbc:postgresql://host.docker.internal:5432/mydb \
    -e FLYWAY_USER=postgres \
    -e FLYWAY_PASSWORD=<YOUR_PASSWORD> \
    ghcr.io/duyhenryer/flyway:latest \
    info
```

## Development

### Building Custom Images

Images are defined using YAML configuration files in the `images/` directory. The structure follows Apko's configuration format:

```yaml
contents:
  repositories:
    - https://packages.wolfi.dev/os
  keyring:
    - https://packages.wolfi.dev/os/wolfi-signing.rsa.pub
  packages:
    - bash
    - your-tool

entrypoint:
  command: /usr/bin/your-tool

accounts:
  run-as: 65532

archs:
  - amd64
  - arm64
```

### Creating Melange Signing Keys

To sign APK packages, create a Melange key:

**Using Docker:**
```bash
docker run --rm -v $PWD:/work cgr.dev/chainguard/melange keygen
```

**Using Local Installation:**
```bash
# Install Melange
brew install melange
# or
go install chainguard.dev/melange@latest

# Generate key
melange keygen
```

This creates:
- Private key: `melange.rsa`
- Public key: `melange.rsa.pub` (committed to repository)

**Building Signed Packages:**
```bash
docker run --rm --privileged \
  -v "$PWD":/work \
  cgr.dev/chainguard/melange \
  --arch x86_64 \
  --signing-key melange.rsa \
  build packages/package-apkrane.yaml
```

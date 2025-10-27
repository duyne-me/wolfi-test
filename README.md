# Wolfi Container Images

> Minimal, secure container images built with WolfiOS, Apko, and Melange

[![Build Images](https://github.com/duyne-me/wolfi-test/actions/workflows/build-images.yml/badge.svg)](https://github.com/duyne-me/wolfi-test/actions/workflows/build-images.yml)
[![Build Package](https://github.com/duyne-me/wolfi-test/actions/workflows/build_package.yml/badge.svg)](https://github.com/duyne-me/wolfi-test/actions/workflows/build_package.yml)

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
│   └── image-kubectl.yaml
├── packages/              # APK package definitions
│   ├── package-apkrane.yaml
│   └── melange-image.yaml
├── docs.md               # Apko & Melange concepts
├── melange.rsa.pub       # Public signing key
└── README.md
```

## Available Images

| Image | Purpose | Base Packages | Registry |
|-------|---------|---------------|----------|
| **Renovate** | Renovate bot with Node.js, Git, Flux CLI | bash, flux, git, renovate | `ghcr.io/duyne-me/renovate:latest` |
| **Apko** | Apko tool with Bash utilities | apko, bash, coreutils, grep | `ghcr.io/duyne-me/apko:latest` |
| **Docker-in-Docker** | Docker-in-Docker with Kubernetes tools | docker, kubectl, kind | `ghcr.io/duyne-me/docker-dind:latest` |
| **AWS CLI** | AWS CLI with common utilities | aws-cli, bash, vim | `ghcr.io/duyne-me/aws-cli:latest` |
| **kubectl** | Kubernetes CLI tool | kubectl, bash | `ghcr.io/duyne-me/kubectl:latest` |

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
    ghcr.io/duyne-me/renovate:latest --version

# Test with Flux CLI
podman run --rm --pull=always \
    --entrypoint=/usr/local/bin/flux \
    ghcr.io/duyne-me/renovate:latest version
```

### Test Apko

```bash
podman run --rm --pull=always \
    ghcr.io/duyne-me/apko:latest version
```

### Test Docker-in-Docker

```bash
podman run --rm --pull=always \
    --privileged \
    ghcr.io/duyne-me/docker-dind:latest
```

### Test AWS CLI

```bash
podman run --rm --pull=always \
    ghcr.io/duyne-me/aws-cli:latest --version
```

### Test kubectl

```bash
podman run --rm --pull=always \
    ghcr.io/duyne-me/kubectl:latest version
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

## CI/CD

### Build Images Workflow

- **Trigger**: Push to `main`, manual dispatch, or weekly schedule (Monday 01:30)
- **Process**: Builds all 5 container images using Apko
- **Publish**: Images are published to `ghcr.io/duyne-me/{image-name}:latest`

### Build Package Workflow

- **Trigger**: Push to `main`, tags (vail), manual dispatch, or weekly schedule
- **Process**: 
  1. Builds custom APK package with Melange
  2. Creates container image with the package
  3. Signs image with Cosign
- **Publish**: Images are published with version tags or nightly tags

## Contributing

### Adding New Images

1. Create new image definition file in `images/` directory: `image-{tool}.yaml`
2. Follow the naming convention: `image-{tool}.yaml`
3. Add build job in `.github/workflows/build-images.yml`
4. Update this README with image details

### Naming Conventions

- **File names**: `image-{tool}.yaml` or `package-{name}.yaml`
- **Image tags**: `ghcr.io/duyne-me/{tool}:latest`
- **Job names**: `build-{tool}` or `build-{name}`

### Testing Requirements

- Test images locally with Podman before committing
- Ensure all archs (amd64, arm64) work correctly
- Verify entrypoint and cmd execute properly
- Check file permissions and user accounts

## License

Apache-2.0

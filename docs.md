## What is Melange? (APK Packaging)

- `Melange` is a tool that helps you compile and package your application into an APK (Alpine Package Keeper) — the package format used by Alpine Linux.
- `Melange` works based on a configuration file `melange.yaml` which specifies:
    - Package name and version
    - Source files and dependencies
    - How to build the application (e.g., Go, Python, Rust builds)

- The result is a `.apk` file containing the compiled application along with metadata.
- `Melange` also supports signing APK packages to ensure integrity and authenticity.
- `Example`: If you have Go source code, `Melange` will compile it into a binary and package it into an `.apk` as a standard Alpine package.


## What is Apko? (Container Image Creation)
- `Apko` is a tool that creates OCI-compliant container images from existing APK packages.
- You don't need to write a `Dockerfile`. Instead, you provide a YAML configuration file to `Apko` which defines:
    - Base image (usually `Alpine` or Wolfi base images)
    - `APK` packages to install (including the ones you built with Melange plus necessary libraries)
    - Metadata such as entrypoint, user, environment variables, etc.

Apko builds a container image containing a minimal OS `(Alpine/Wolfi)` plus your APK package.

## Melange and Apko

| Task                       | Tool      | Result                                      |
|----------------------------|-----------|---------------------------------------------|
| Compile and package app    | Melange   | Creates an `.apk` file containing your app  |
| Create OCI container image | Apko      | Creates a container image from the `.apk`   |
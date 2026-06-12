# Container Machine Images

This repository contains the Dockerfiles meant to build the images used by [Apple's container machine](https://github.com/apple/container/blob/main/docs/container-machine.md). Main requirement for Docker/OCI image to work is to have `/sbin/init`, which is used by container machine to run the container as a VM.

## Available Images

Each Dockerfile is named after its package manager family and uses a build argument (`BASE_IMAGE`) for the OCI image name:tag:

| Dockerfile | Package Manager | Default Base Image |
|------------|-----------------|-------------------|
| `Dockerfile.apt` | apt (Debian/Ubuntu) | `debian:testing-slim` |
| `Dockerfile.dnf` | dnf (Fedora/RHEL-alikes) | `almalinux:10` |
| `Dockerfile.microdnf` | microdnf (RHEL-minimal) | `almalinux:10-minimal` |
| `Dockerfile.zypper` | zypper (openSUSE) | `opensuse/leap:16` |
| `Dockerfile.apk` | apk (Alpine Linux) | `alpine:3.24` |

### Looking for Arch Linux?

Arch Linux does not have a supported arm64 release, so no Dockerfile is provided for pacman here.
For similar bleeding-edge distros that work on arm64, consider using Debian sid or Fedora Rawhide:

```bash
# Debian sid
container build -t local/sid -f Dockerfile.apt --build-arg BASE_IMAGE=debian:sid-slim

# Fedora Rawhide
container build -t local/rawhide -f Dockerfile.dnf --build-arg BASE_IMAGE=fedora:rawhide
```

## Building the images

To build the images, you can use `container build` command from the root of this repository:

```bash
# Using default base image
container build -t local/<name> -f Dockerfile.<name>

# Overriding the base image (e.g., different version)
container build -t local/<name> -f Dockerfile.<name> --build-arg BASE_IMAGE=ubuntu:24.04
```

where `<name>` is the package manager family (e.g., `apt`, `dnf`, `microdnf`, `zypper`, `apk`).

> [!Note]
> You can use any tag you want for the `-t` flag, I chose `local/<name>` to make it clear that these images are not coming from a Docker registry. You can omit the `local/` prefix if you want, but make sure to use the same name when running the container machine.

## Using the images

To use the images, just run the container machine, providing the image name you used to build the image:

```bash
container machine create local/<name> --name <machine-name>
container machine run -n <machine-name>
```

## Examples

```bash
# Debian Testing (apt)
container build -t local/apt -f Dockerfile.apt
container machine create local/apt --name debian-machine
container machine run -n debian-machine

# AlmaLinux (dnf)
container build -t local/dnf -f Dockerfile.dnf
container machine create local/dnf --name alma-machine
container machine run -n alma-machine

# AlmaLinux-minimal (microdnf)
container build -t local/microdnf -f Dockerfile.microdnf
container machine create local/microdnf --name ubi-micro-machine
container machine run -n ubi-micro-machine

# openSUSE Leap 16 (zypper)
container build -t local/zypper -f Dockerfile.zypper
container machine create local/zypper --name opensuse-machine
container machine run -n opensuse-machine

# Alpine Linux 3.24 (apk)
container build -t local/apk -f Dockerfile.apk
container machine create local/apk --name alpine-machine
container machine run -n alpine-machine
```

## Overriding the base image

All Dockerfiles accept a `BASE_IMAGE` build argument to customize the base OCI image:

```bash
# Ubuntu 26.04 instead of Debian Testing
container build -t local/apt -f Dockerfile.apt --build-arg BASE_IMAGE=ubuntu:26.04

# Rocky Linux 9 instead of AlmaLinux 10
container build -t local/dnf -f Dockerfile.dnf --build-arg BASE_IMAGE=rockylinux/rockylinux:9

# Alpine 3.20 instead of 3.24
container build -t local/apk -f Dockerfile.apk --build-arg BASE_IMAGE=alpine:3.20
```

## Contributing

If you want to contribute to this repository, feel free to open a pull request with your changes. I am open to any suggestions and improvements :smiley:

---

I am not affiliated with Apple, and this repository is not an official Apple product. The images provided here are meant for testing and development purposes only, and may not be suitable for production use. Use at your own risk.

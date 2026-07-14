# Container Machine Images

This repository contains the Dockerfiles meant to build the images used by [Apple's container machine](https://github.com/apple/container/blob/main/docs/container-machine.md). Main requirement for Docker/OCI image to work is to have `/sbin/init`, which is used by container machine to run the container as a VM.

## Building the images

To build the images, you can use `container build` command from the root of this repository:

```bash
container build -t local/<name> -f Dockerfile.<name>
```

where `<name>` is the name of the image you want to build (e.g. `ubuntu`).

> [!Note]
> You can use any tag you want for the `-t` flag, I chose `local/<name>` to make it clear that these images are not comming from a Docker registry. You can ommit the `local/` prefix if you want, but make sure to use the same name when running the container machine.

## Using the images

To use the images, just run the container machine, providing the image name you used to build the image:

```bash
container machine create local/<name> --name <machine-name>
container machine run -n <machine-name>
```

## Example

To build and run the Ubuntu image, you can use the following commands:

```bash
container build -t local/ubuntu -f Dockerfile.ubuntu
container machine create local/ubuntu --name ubuntu-machine
container machine run -n ubuntu-machine
```

## Contributing

If you want to contribute to this repository, feel free to open a pull request with your changes. I am open to any suggestions and improvements :smiley:

---

I am not affiliated with Apple, and this repository is not an official Apple product. The images provided here are meant for testing and development purposes only, and may not be suitable for production use. Use at your own risk.

# Jetson-Linux-PyTorch

## Overview

This repository focuses on allowing you to build PyTorch (and other packages) for ARM64 devices (e.g. Nvidia Jetson Nano) on an AMD64 machine. Removing the need of building these packages on those small devices themselves.

## Requirements

While working on AI Models to run on the Nvidia Jetson Nano, I had a need for a Python version > 3.6. After doing some research, it appeared that Nvidia only offers Python 3.6 for the ARM64 (aarch64) architecture, creating the need to compile PyTorch from source. Another complexity added to this was that I needed CUDA support to utilize the GPU on the Nvidia Jetson device.

Summarized Requirements:

* Python > 3.6
* CUDA support
* ARM64

## Arguments

* V_CUDA_MAJOR=10
* V_CUDA_MINOR=2
* V_L4T_MAJOR=32
* V_L4T_MINOR=6
* V_PYTHON_MAJOR=3
* V_PYTHON_MINOR=9
* V_PYTORCH=v1.10.0
* V_PYTORCHVISION=v0.11.1
* V_PYTORCHAUDIO=v0.10.0

## Building PyTorch

### Prerequisites

Before we get started we need to ensure that we can emulate ARM64 on our AMD64 machine. We do this by install `qemu` and configuring it:

```bash
# Install QEMU
sudo apt install binfmt-support qemu qemu-user-static

# Configure QEMU to enable execution of different multi-architecture containers by QEMU and binfmt_misc
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```

### Building PyTorch

After our prerequisites are done, we can compile PyTorch by starting up the docker build process:

```bash
docker buildx build --platform=linux/arm64 --progress=plain --output type=local,dest=. -f Dockerfile.jetson .
```

## References

Thanks a lot to the code repositories and authors below:

* [https://github.com/soerensen3/buildx-pytorch-jetson/blob/master/Dockerfile](https://github.com/soerensen3/buildx-pytorch-jetson/blob/master/Dockerfile)
* [https://github.com/rnanosaur/nanosaur_perception/blob/19653bbbb5529bb2e803b7d122512548f7bc6918/Dockerfile](https://github.com/rnanosaur/nanosaur_perception/blob/19653bbbb5529bb2e803b7d122512548f7bc6918/Dockerfile)
* [https://medium.com/@artur.klauser/building-multi-architecture-docker-images-with-buildx-27d80f7e2408](https://medium.com/@artur.klauser/building-multi-architecture-docker-images-with-buildx-27d80f7e2408)
* [https://stackoverflow.com/questions/65365797/docker-buildx-exec-user-process-caused-exec-format-error](https://stackoverflow.com/questions/65365797/docker-buildx-exec-user-process-caused-exec-format-error)
* [https://github.com/pytorch/pytorch/blob/c371542efc31b1abfe6f388042aa3ab0cef935f2/cmake/Modules_CUDA_fix/upstream/FindCUDA/select_compute_arch.cmake](https://github.com/pytorch/pytorch/blob/c371542efc31b1abfe6f388042aa3ab0cef935f2/cmake/Modules_CUDA_fix/upstream/FindCUDA/select_compute_arch.cmake)

## Extra

We can potentially utilize the GPG Public Key of the Nvidia Jetson through this:

```bash
# Copy over GPG Public Key of Jetson
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
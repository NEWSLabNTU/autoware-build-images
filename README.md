# Autoware Build Images

This directory is a Git submodule that links to the [autoware-build-images](https://github.com/NEWSLabNTU/autoware-build-images) repository.

## Purpose

These Docker images are pre-configured build environments for creating Debian packages from Autoware colcon workspaces. They include:

- ROS 2 Humble installation
- Autoware dependencies and tools
- Build essentials for Debian packaging
- Platform-specific optimizations

## Available Images

The repository provides Dockerfiles for different platforms:

- **amd64** - Standard x86_64 architecture
- **arm64** - ARM 64-bit architecture
- **jetpack** - NVIDIA Jetson platforms with CUDA/TensorRT support

## Usage

To use these images with colcon2deb:

1. **Clone with submodules**:
   ```bash
   git clone --recursive https://github.com/NEWSLabNTU/colcon2deb.git
   ```

2. **Or initialize submodules in existing clone**:
   ```bash
   git submodule update --init --recursive
   ```

3. **Reference in your config.yaml**:
   ```yaml
   docker:
     dockerfile: ./docker/2025.02/amd64/Dockerfile
   ```

## Direct Repository Access

For the latest images or to contribute, visit the main repository:
https://github.com/NEWSLabNTU/autoware-build-images

## Note

This submodule is linked to a specific commit of the autoware-build-images repository to ensure build reproducibility. To update to the latest images, update the submodule reference.

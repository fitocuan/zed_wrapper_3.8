# Docker

This folder contains a list of Dockerfile files to build Docker images ready to start the nodes of the *ZED ROS2 Wrapper*:

* `Dockerfile.u22-cu117-humble-release`: desktop image for ROS2 Humble, running on Ubuntu 22.04, with CUDA 11.7. The ZED Wrapper is cloned from the master branch and compiled.
* `Dockerfile.u22-cu117-humble-devel`: development desktop image for ROS2 Humble, running on Ubuntu 22.04, with CUDA 11.7. The ZED Wrapper is copied from the source file of the current branch and compiled. This is useful to create a Docker image of a branch to be tested before merging it in the master branch.

## Build the Docker images

Choose a name for the image and replace `<image_tag>` with it, e.g. `zed-ubuntu22.04-cuda11.7-ros2-humble`

### Release image

The Release image internally clones the master branch of this repository to build the ZED ROS2 Wrapper code.

Build the image for desktop:
```bash
docker build -t "<image_tag>" -f Dockerfile.u22-cu117-humble-release .
```

or build the image for Jetson:

```bash
docker build -t "<image_tag>" -f Dockerfile.l4t35_1-humble-release .
```

### Devel image

The devel image internally needs the source code of the current branch. For this reason we must first copy the source to a temporary folder reachable while building the Docker image. The folder can be removed when the Docker image is ready.

Create a temporary `tmp_sources` folder for the sources and copy the files:

```bash
mkdir -p ./tmp_sourcesmkdir -p ./tmp_sources
cp -cp -r ../zed* ./tmp_sourcesr ../zed* ./tmp_sources
```

Build the image for desktop:

```bash
docker build -t "<image_tag>" -f Dockerfile.u22-cu117-humble-devel .
```

or build the image for Jetson:

```bash
docker build -t "<image_tag>" -f Dockerfile.l4t35_1-humble-devel .
```

Remove the temporary sources to avoid future compiling issues:

```bash
rm -r ./tmp_sources
```

**Note:** it is important that the name of the temporary folder is `tmp_sources` because it is used internally by the Dockerfile.

## Run the Docker image

It is important that the NVIDIA drivers are correctly accessible from the Docker image to run the ZED SDK code on the GPU.

The following command starts an interactive BaSH session:

```bash
docker run --runtime nvidia -it --privileged --ipc=host --pid=host -e DISPLAY \
  -v /dev/shm:/dev/shm -v /tmp/.X11-unix/:/tmp/.X11-unix \
  <image_tag>
```

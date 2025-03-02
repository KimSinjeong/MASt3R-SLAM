# MASt3r-SLAM Docker Image Build from Dockerfile
This document explains how to build docker image for MASt3r-SLAM. This document assumes that readers' systems meet following requirements:
- x86-64 (amd64) architecture
- GPU that supports CUDA 11.8
- Ubuntu

### Dependencies
- [Docker](https://docs.docker.com/engine/install/)
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#setting-up-nvidia-container-toolkit)

### Building Docker Image
Download the attached Dockerfile and run the below command at where the Dockerfile is.
```bash
docker build -t="mast3r-slam:latest" .
```

Run the built Docker image with the following command.
```bash
docker run \                       
  --rm \
  -it \
  --gpus all \
  --shm-size 50G \
  --device=/dev/dri:/dev/dri \
  --device-cgroup-rule "c 81:* rmw" \
  --device-cgroup-rule "c 189:* rmw" \
  -v /tmp/.X11-unix:/tmp/.X11-unix -v /dev:/dev \
  -e DISPLAY=$DISPLAY \
  -e QT_X11_NO_MITSHM=1 \
  mast3r-slam:latest \
  bash
```

To run a GUI application on the container, you should allow X server connection from the host side:
```bash
xhost +local:*
```
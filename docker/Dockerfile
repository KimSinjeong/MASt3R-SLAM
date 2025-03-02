FROM nvidia/cuda:11.8.0-devel-ubuntu20.04

ENV DEBIAN_FRONTEND=noninteractive
ENV DOCKER_BUILDING=1

RUN apt update && apt install -y \
    git \
    ninja-build \
    build-essential \
    wget \
    software-properties-common \
    lsb-core \
    cmake \
    curl

# Install librealsense
RUN mkdir -p /etc/apt/keyrings
RUN curl -sSf https://librealsense.intel.com/Debian/librealsense.pgp | tee /etc/apt/keyrings/librealsense.pgp > /dev/null

RUN echo "deb [signed-by=/etc/apt/keyrings/librealsense.pgp] https://librealsense.intel.com/Debian/apt-repo `lsb_release -cs` main" | \
    tee /etc/apt/sources.list.d/librealsense.list
RUN apt update && \
    apt install -y --fix-missing \
    librealsense2-dkms \
    librealsense2-utils

# Install python3.11
RUN add-apt-repository ppa:deadsnakes/ppa
RUN apt update && \
    apt install -y --fix-missing\
    python3.11-dev

RUN update-alternatives --install /usr/bin/python python /usr/bin/python3.11 1

RUN wget https://bootstrap.pypa.io/get-pip.py
RUN python get-pip.py

# Install pytorch
RUN python -m pip install torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu118

# Download MASt3R-SLAM
RUN git clone https://github.com/rmurai0610/MASt3R-SLAM.git --recursive

# Enable GUI
RUN apt-get update \
  && apt-get install -y -qq --no-install-recommends \
    libglvnd0 \
    libgl1 \
    libglx0 \
    libegl1 \
    libxext6 \
    libx11-6 \
    libusb-1.0-0-dev \
    ffmpeg \
  && rm -rf /var/lib/apt/lists/*

RUN if test -f "/usr/lib/x86_64-linux-gnu/libGL.so"; then \
    echo "libGL.so exists" \
    else \
    ln -s /usr/lib/x86_64-linux-gnu/libGL.so.1 /usr/lib/x86_64-linux-gnu/libGL.so; \
    fi

# Env vars for the nvidia-container-runtime.
ENV NVIDIA_VISIBLE_DEVICES=all
ENV NVIDIA_DRIVER_CAPABILITIES=all

WORKDIR /MASt3R-SLAM/
# Warning: Currently working with only Turing and later architectures 
ENV TORCH_CUDA_ARCH_LIST="Turing Ampere+Tegra Ampere Ada Hopper"
RUN python -m pip install -e thirdparty/mast3r && \
    python -m pip install -e thirdparty/in3d && \
    python -m pip install --no-build-isolation -e .

ENV DOCKER_BUILDING=0

RUN mkdir -p checkpoints/
RUN wget https://download.europe.naverlabs.com/ComputerVision/MASt3R/MASt3R_ViTLarge_BaseDecoder_512_catmlpdpt_metric.pth -P checkpoints/
RUN wget https://download.europe.naverlabs.com/ComputerVision/MASt3R/MASt3R_ViTLarge_BaseDecoder_512_catmlpdpt_metric_retrieval_trainingfree.pth -P checkpoints/
RUN wget https://download.europe.naverlabs.com/ComputerVision/MASt3R/MASt3R_ViTLarge_BaseDecoder_512_catmlpdpt_metric_retrieval_codebook.pkl -P checkpoints/

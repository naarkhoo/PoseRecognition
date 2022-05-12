FROM ubuntu:18.04

LABEL maintainer="alireza"
LABEL description="CPU-only version of OpenPose. Not slimmed for production."
LABEL version="1.1"

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y \
        wget nano apt-utils lsb-core cmake git \
        libopencv-dev \
        protobuf-compiler \
        libprotobuf-dev \
        libgoogle-glog-dev \
        libboost-all-dev \
        hdf5-tools \
        libhdf5-dev \
        libatlas-base-dev \
        libglib2.0-0 libsm6 libxext6 libxrender1 libgl1-mesa-glx

RUN git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git

WORKDIR openpose

# Latest commit as of 18 Jan 22.
# Change if you need to, but be aware that dependencies may have changed.
RUN git checkout 6f0b8868bc4833b4a6156f020dd6d486dcf8a976

WORKDIR scripts/ubuntu

RUN sed -i 's/\<sudo -H\>//g' install_deps.sh; \
    sed -i 's/\<sudo\>//g' install_deps.sh; \
    sync; sleep 1;

WORKDIR /openpose/build

# Downloads all available models. You can reduce image size by being more selective.
RUN cmake -DGPU_MODE:String=CPU_ONLY \
          -DOWNLOAD_BODY_25_MODEL:Bool=ON \
          -DDOWNLOAD_BODY_MPI_MODEL:Bool=ON \
          -DDOWNLOAD_BODY_COCO_MODEL:Bool=ON \
          -DDOWNLOAD_FACE_MODEL:Bool=ON \
          -DDOWNLOAD_HAND_MODEL:Bool=ON \
          -DUSE_MKL:Bool=OFF \
          ..

# you may find that you need to adjust this.
RUN make -j$((`nproc`+1))

#replace cmake as old version has CUDA variable bugs
RUN wget https://github.com/Kitware/CMake/releases/download/v3.16.0/cmake-3.16.0-Linux-x86_64.tar.gz && \
tar xzf cmake-3.16.0-Linux-x86_64.tar.gz -C /opt && \
rm cmake-3.16.0-Linux-x86_64.tar.gz
ENV PATH="/opt/cmake-3.16.0-Linux-x86_64/bin:${PATH}"

WORKDIR /openpose/build
RUN cmake -DBUILD_PYTHON=ON .. && make -j `nproc`

RUN apt-get install python3-pip -y 
RUN pip3 install --upgrade pip
RUN pip3 install numpy opencv-python

RUN apt-get install -y '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev 
RUN apt-get install -y libxcb-util-dev
ENV QT_DEBUG_PLUGINS=1
RUN apt-get install -y qt5-default
WORKDIR /openpose

FROM balenalib/jetson-nano-ubuntu:bionic as buildstep

WORKDIR /usr/src/app

# Files from the SDK
COPY ./cuda-repo-l4t-10-0-local-10.0.326_1.0-1_arm64.deb .

ENV DEBIAN_FRONTEND noninteractive

RUN \
    dpkg -i cuda-repo-l4t-10-0-local-10.0.326_1.0-1_arm64.deb && \
    apt-key add /var/cuda-repo-10-0-local-10.0.326/*.pub && \
    apt-get update && \
    apt-get install -y cuda-compiler-10-0 \
    cuda-samples-10-0 \
    lbzip2 \
    git wget unzip && \
    rm -rf ./*.deb

# Free up a little space
RUN rm -rf /usr/local/cuda-10.0/doc

# ------------------------------------------------------------------

FROM balenalib/jetson-nano-ubuntu:bionic as final

WORKDIR /usr/src/app

COPY --from=buildstep /usr/local/cuda-10.0 /usr/local/cuda-10.0

# Minimum CUDA runtime libraries
COPY --from=buildstep /usr/lib/aarch64-linux-gnu /usr/lib/aarch64-linux-gnu

# set paths
ENV CUDA_HOME=/usr/local/cuda-10.0/
ENV LD_LIBRARY_PATH=${CUDA_HOME}/lib64
ENV PATH=${CUDA_HOME}/bin:${PATH}
ENV UDEV=1
    
COPY ./nvidia_drivers.tbz2 .
COPY ./config.tbz2 .

# Prepare minimum of runtime libraries
RUN apt-get update && apt-get install -y lbzip2 pkg-config && \
    tar xjf nvidia_drivers.tbz2 -C / && \
    tar xjf config.tbz2 -C / --exclude=etc/hosts --exclude=etc/hostname && \
    echo "/usr/lib/aarch64-linux-gnu/tegra" > /etc/ld.so.conf.d/nvidia-tegra.conf && ldconfig && \
    rm -rf *.tbz2


CMD [ "sleep", "infinity" ]

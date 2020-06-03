FROM balenalib/jetson-nano-ubuntu:bionic as buildstep

WORKDIR /usr/src/app

# Files from the SDK
COPY ./cuda-repo-l4t-10-0-local-10.0.326_1.0-1_arm64.deb .
COPY ./libcudnn7_7.6.3.28-1+cuda10.0_arm64.deb .
COPY ./libcudnn7-dev_7.6.3.28-1+cuda10.0_arm64.deb .
COPY ./libcudnn7-doc_7.6.3.28-1+cuda10.0_arm64.deb .

ENV DEBIAN_FRONTEND noninteractive

## Install runtime & build libraries and prereqs
RUN \
    dpkg -i cuda-repo-l4t-10-0-local-10.0.326_1.0-1_arm64.deb && \
    apt-key add /var/cuda-repo-10-0-local-10.0.326/*.pub && \
    dpkg -i libcudnn7_7.6.3.28-1+cuda10.0_arm64.deb \
    libcudnn7-dev_7.6.3.28-1+cuda10.0_arm64.deb \
    libcudnn7-doc_7.6.3.28-1+cuda10.0_arm64.deb && \
    apt-get update && \
    apt-get install -y cuda-compiler-10-0 \
    cuda-samples-10-0 \
    lbzip2 \
    git wget unzip \
    cmake automake build-essential checkinstall \
    autoconf libtool \
    libgtk2.0-dev pkg-config libgtk-3-dev \
    libavcodec-dev libavformat-dev libeigen3-dev\
    libglew-dev libtbb2 libtbb-dev \
    libv4l-dev v4l-utils qv4l2 v4l2ucp \
    libxine2-dev libatlas-base-dev libopenblas-dev \
    liblapack-dev liblapacke-dev gfortran \
    libgstreamer1.0-0 libswscale-dev \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-libav \
    gstreamer1.0-doc \
    gstreamer1.0-tools \
    libgstreamer1.0-dev \
    libgstreamer-plugins-base1.0-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    liblcms2-dev \
    zlib1g-dev \
    ffmpeg \
    libdc1394-22-dev -y --no-install-recommends && \
    rm -rf ./*.deb && \
    dpkg --remove cuda-repo-l4t-10-0-local-10.0.326 && \
    dpkg -P cuda-repo-l4t-10-0-local-10.0.326 && \
    echo "/usr/lib/aarch64-linux-gnu/tegra" > /etc/ld.so.conf.d/nvidia-tegra.conf \
    && ldconfig && \
    wget https://github.com/opencv/opencv/archive/4.3.0.zip && \
    unzip 4.3.0.zip && rm 4.3.0.zip
 
    # Python 3.6.9
    RUN apt-get install -y libreadline-gplv2-dev libncursesw5-dev libssl1.0-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev && \
        wget https://www.python.org/ftp/python/3.6.9/Python-3.6.9.tgz && \
        tar -xvf Python-3.6.9.tgz && \
        cd Python-3.6.9 && \
        ./configure && \
        make && \
        sudo checkinstall

    # Python 3 OpenCV prereqs
    RUN apt-get install -y python3.6-dev python3-testresources && \
        rm -f /usr/src/app/get-pip.py && \
        wget https://bootstrap.pypa.io/get-pip.py -O /usr/src/app/get-pip.py && \
        python3 /usr/src/app/get-pip.py && \
        pip3 install protobuf cython && \
        pip3 install -U numpy matplotlib

    # Install OpenCV and modules
    ENV OPENCV_RELEASE_TAG 4.3.0

    RUN \
    wget https://github.com/opencv/opencv_contrib/archive/${OPENCV_RELEASE_TAG}.zip -O opencv_modules.${OPENCV_RELEASE_TAG}.zip && \
    unzip opencv_modules.${OPENCV_RELEASE_TAG}.zip && rm opencv_modules.${OPENCV_RELEASE_TAG}.zip && \
    mkdir -p opencv-${OPENCV_RELEASE_TAG}/build && cd opencv-${OPENCV_RELEASE_TAG}/build && \
    cmake -D WITH_CUDA=ON \
          -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-10.0 \
          -D CUDA_ARCH_BIN="5.3" \
          -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-${OPENCV_RELEASE_TAG}/modules \
          -D CMAKE_INSTALL_PREFIX=/usr/local \
          -D CMAKE_BUILD_TYPE=Release \
          -D PYTHON3_EXECUTABLE=$(which python3.6) \
          -D PYTHON3_INCLUDE_DIR=$(python3.6 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
          -D PYTHON3_INCLUDE_DIR2=/usr/include/aarch64-linux-gnu/python3.6m \
          -D PYTHON3_LIBRARY=/usr/lib/python3.6/config-3.6m-aarch64-linux-gnu/libpython3.6.so \
          -D PYTHON3_NUMPY_INCLUDE_DIR=$(python3 -c "import numpy; print(numpy.get_include())") \
          -D INSTALL_PYTHON_EXAMPLES=ON \
          -D BUILD_opencv_python3=ON \
          -D BUILD_opencv_python2=OFF \
          -D WITH_GSTREAMER=ON \
          -D WITH_GSTREAMER_0_10=OFF \
          -D WITH_TBB=ON \
          -D WITH_LIBV4L=ON \
          -D WITH_FFMPEG=ON -DOPENCV_GENERATE_PKGCONFIG=ON .. && make -j5 && make install && \
    cp /usr/src/app/opencv-${OPENCV_RELEASE_TAG}/build/bin/opencv_version /usr/src/app/ && \
    rm -rf /usr/src/app/opencv_contrib-${OPENCV_RELEASE_TAG}

# ------------------------------------------------------------------

FROM balenalib/jetson-nano-ubuntu:bionic as final

WORKDIR /usr/src/app

COPY --from=buildstep /usr/local/cuda-10.0 /usr/local/cuda-10.0

# Minimum CUDA runtime libraries
COPY --from=buildstep /usr/lib/aarch64-linux-gnu /usr/lib/aarch64-linux-gnu

# OpenCV
COPY --from=buildstep /usr/local/lib /usr/local/lib
COPY --from=buildstep /usr/local/include/opencv4 /usr/local/include/opencv4

# OpenCV Python libraries
COPY --from=buildstep /usr/local/lib/python3.6/site-packages/cv2 /usr/local/lib/python3.6/site-packages/cv2

# CUDNN
COPY --from=buildstep /usr/include/cudnn.h /usr/include/cudnn.h
COPY --from=buildstep /etc/alternatives/libcudnn* /etc/alternatives/
COPY --from=buildstep /usr/include/aarch64-linux-gnu/cudnn_v7.h /usr/include/aarch64-linux-gnu/cudnn_v7.h

# set paths
ENV CUDA_HOME=/usr/local/cuda-10.0
ENV LD_LIBRARY_PATH=${CUDA_HOME}/lib64:/usr/lib/aarch64-linux-gnu/lapack
ENV PATH=${CUDA_HOME}/bin:${PATH}
ENV UDEV=1
ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
ENV LAPACK=/usr/lib/aarch64-linux-gnu/lapack/liblapack.so
ENV PYTHONPATH=/usr/local/lib/python3.6/site-packages
    
COPY ./nvidia_drivers.tbz2 .
COPY ./config.tbz2 .

ENV DEBIAN_FRONTEND noninteractive

# For Python3.6
RUN apt update && apt install -y software-properties-common

# Prepare minimum of runtime libraries and cv2 prereqs
RUN \
    apt-get update && apt-get install -y lbzip2 pkg-config git wget \
    libatlas-base-dev libopenblas-dev liblapack-dev liblapacke-dev gfortran && \
    tar xjf nvidia_drivers.tbz2 -C / && \
    tar xjf config.tbz2 -C / --exclude=etc/hosts --exclude=etc/hostname && \
    echo "/usr/lib/aarch64-linux-gnu/tegra" > /etc/ld.so.conf.d/nvidia-tegra.conf && \
    echo "/usr/local/cuda-10.0/targets/aarch64-linux/lib" > /etc/ld.so.conf.d/cuda-10-0.conf && \
    ldconfig && \
    rm -rf *.tbz2

COPY check_cuda.py .
COPY check_opencv.py .

CMD [ "sleep", "infinity" ]

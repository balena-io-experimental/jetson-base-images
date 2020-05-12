FROM balenalib/jetson-nano-ubuntu:bionic

WORKDIR /usr/src/app

# File from the SDK
COPY ./cuda-repo-l4t-10-0-local-10.0.326_1.0-1_arm64.deb .

ENV DEBIAN_FRONTEND noninteractive

## Install runtime & build libraries and prereqs
RUN \
    dpkg -i cuda-repo-l4t-10-0-local-10.0.326_1.0-1_arm64.deb && \
    apt-key add /var/cuda-repo-10-0-local-10.0.326/*.pub && \
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
    
    # Python 2.7.17 for OpenCV build
    RUN wget https://www.python.org/ftp/python/2.7.17/Python-2.7.17.tgz && \
        tar xzf Python-2.7.17.tgz && \
        cd Python-2.7.17 && \
        ./configure --enable-optimizations && \
        make altinstall

    # Python 2 OpenCV prereqs (uses same get-pip.py script as above)
    RUN apt-get install -y python2.7-dev && \
        python2.7 /usr/src/app/get-pip.py && \
        pip install numpy

    # Install OpenCV and modules
    RUN \
    wget https://github.com/opencv/opencv_contrib/archive/4.3.0.zip -O opencv_modules.4.3.0.zip && \
    unzip opencv_modules.4.3.0.zip && rm opencv_modules.4.3.0.zip && \
    mkdir -p opencv-4.3.0/build && cd opencv-4.3.0/build && \
    cmake -D WITH_CUDA=ON \
          -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-10.0 \
          -D CUDA_ARCH_BIN="5.3" \
          -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.3.0/modules \
          -D CMAKE_INSTALL_PREFIX=/usr/local \
          -D CMAKE_BUILD_TYPE=Release \
          -D PYTHON3_EXECUTABLE=$(which python3.6) \
          -D PYTHON2_EXECUTABLE=$(which python2.7) \
          -D PYTHON2_INCLUDE_DIR=$(python2.7 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
          -D PYTHON2_INCLUDE_DIR2=/usr/include/aarch64-linux-gnu/python2.7 \
          -D PYTHON3_INCLUDE_DIR=$(python3.6 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
          -D PYTHON3_INCLUDE_DIR2=/usr/include/aarch64-linux-gnu/python3.6m \
          -D PYTHON2_LIBRARY=/usr/lib/python2.7/config-aarch64-linux-gnu/libpython2.7.so \
          -D PYTHON3_LIBRARY=/usr/lib/python3.6/config-3.6m-aarch64-linux-gnu/libpython3.6.so \
          -D PYTHON2_NUMPY_INCLUDE_DIR=$(python2.7 -c "import numpy; print(numpy.get_include())") \
          -D PYTHON3_NUMPY_INCLUDE_DIR=$(python3 -c "import numpy; print(numpy.get_include())") \
          -D INSTALL_PYTHON_EXAMPLES=ON \
          -D BUILD_opencv_python3=ON \
          -D BUILD_opencv_python2=OFF \
          -D WITH_GSTREAMER=ON \
          -D WITH_GSTREAMER_0_10=OFF \
          -D WITH_TBB=ON \
          -D WITH_LIBV4L=ON \
          -D WITH_FFMPEG=ON .. && make -j8 && make install && \
    cp /usr/src/app/opencv-4.3.0/build/bin/opencv_version /usr/src/app/ && \
    rm -rf /usr/src/app/opencv_contrib-4.3.0


# set paths
ENV CUDA_HOME=/usr/local/cuda-10.0/
ENV LD_LIBRARY_PATH=${CUDA_HOME}lib64:/usr/lib/aarch64-linux-gnu/lapack
ENV PATH=${CUDA_HOME}bin:${PATH}
ENV UDEV=1
ENV LAPACK=/usr/lib/aarch64-linux-gnu/lapack/liblapack.so
    
COPY ./nvidia_drivers.tbz2 .
COPY ./config.tbz2 .

# Prepare minimum of runtime libraries
RUN tar xjf nvidia_drivers.tbz2 -C / && \
    tar xjf config.tbz2 -C / --exclude=etc/hosts --exclude=etc/hostname && \
    echo "/usr/lib/aarch64-linux-gnu/tegra" > /etc/ld.so.conf.d/nvidia-tegra.conf && ldconfig && \
    rm -rf *.tbz2

# Free up space
RUN rm -rf /usr/local/cuda-10.0/doc
RUN rm -rf /usr/local/cuda-10.0/samples
RUN rm -rf /usr/src/app/opencv-4.3.0
RUN rm -rf /usr/src/app/Python-2.7.17*
RUN rm -rf /usr/src/app/Python-3.6.9*
RUN apt purge -y python2.7


COPY check_cuda.py .
COPY check_opencv.py .

CMD [ "sleep", "infinity" ]

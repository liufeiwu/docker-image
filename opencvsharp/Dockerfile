FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-bionic

ENV OPENCV_VERSION=4.3.0
ENV ASPNETCORE_URLS=http://*:80

# Update Packages
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse'>/etc/apt/sources.list && \
  echo 'deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse'>>/etc/apt/sources.list && \
  echo 'deb http://security.ubuntu.com/ubuntu xenial-security main'>>/etc/apt/sources.list && \
  apt-get -y update && apt-get -y upgrade

RUN apt-get install -y \
    apt-transport-https \
    software-properties-common \
    wget \
    unzip \
    curl \
    ca-certificates
    #bzip2 \
    #grep sed dpkg 

# Install opencv dependencies
RUN cd ~
RUN apt-get install -y \
    build-essential \
    cmake \
    git \
    gfortran \
    libjpeg8-dev \
    libpng-dev \
    software-properties-common
RUN apt-get install -y \
    libjasper1 \
    libtiff-dev \
    libavcodec-dev \
    libavformat-dev \
    libswscale-dev \
    libdc1394-22-dev \
    libxine2-dev \
    libv4l-dev

RUN cd /usr/include/linux
RUN ln -s -f ../libv4l1-videodev.h videodev.h
RUN cd ~
RUN apt-get install -y \
    libgtk2.0-dev libtbb-dev qt5-default \
    libatlas-base-dev \
    libfaac-dev \
    libmp3lame-dev \
    libtheora-dev \
    libvorbis-dev \
    libxvidcore-dev \
    libopencore-amrnb-dev \
    libopencore-amrwb-dev \
    libavresample-dev \
    x264 \
    v4l-utils

# Setup OpenCV source
RUN wget https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip && \
    unzip ${OPENCV_VERSION}.zip && \
    rm ${OPENCV_VERSION}.zip && \
    mv opencv-${OPENCV_VERSION} opencv

# Setup opencv-contrib Source
RUN wget https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.zip && \
    unzip ${OPENCV_VERSION}.zip && \
    rm ${OPENCV_VERSION}.zip && \
    mv opencv_contrib-${OPENCV_VERSION} opencv_contrib

# Build OpenCV
RUN cd opencv && mkdir build && cd build && \
    cmake \
    -D OPENCV_EXTRA_MODULES_PATH=/opencv_contrib/modules \
    -D CMAKE_BUILD_TYPE=RELEASE \
    -D BUILD_SHARED_LIBS=OFF \
    -D ENABLE_CXX11=ON \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_DOCS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_JAVA=OFF \
    -D BUILD_opencv_app=OFF \
    -D BUILD_opencv_java=OFF \
    -D BUILD_opencv_python=OFF \
    -D BUILD_opencv_ts=OFF \
    -D BUILD_opencv_js=OFF \
    -D WITH_GSTREAMER=OFF \ 
    -D OPENCV_ENABLE_NONFREE=ON \
    .. && make -j2 && make install && ldconfig

# tzdate
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata && \
  ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# Clean apt
RUN apt-get clean -y
FROM ubuntu:22.04

# Install build tools and dependencies notice the specific version of cmake
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        apt-transport-https \
        curl \
        gnupg \
        sudo `# allows dev to install more packages without switching to root or rebuilding container` \
        software-properties-common \
        build-essential \
        libsqlite3-dev \
        libcurl4-openssl-dev \
        libglfw3-dev \
        libuv1-dev \
        libpng-dev \
        libicu-dev \
        libjpeg-turbo8-dev \
        libwebp-dev \
        xvfb \
        clang \
        git \
        ccache \
        ninja-build \
        pkg-config \
        clang-tidy \
   && curl -LO https://github.com/Kitware/CMake/releases/download/v3.24.0/cmake-3.24.0-linux-x86_64.tar.gz \
   && tar -zxvf cmake-3.24.0-linux-x86_64.tar.gz \
   && sudo mv cmake-3.24.0-linux-x86_64 /opt/cmake \
   && sudo ln -s /opt/cmake/bin/cmake /usr/local/bin/cmake \
   && cmake --version

# install gdb for debugging in vscode containers
RUN apt-get update && \
    apt-get install -y \
    libgl1-mesa-glx \
    libgl1-mesa-dri \
    xorg \
    libglu1-mesa \
    gdb \
    gdbserver
    

RUN cmake --version

# Install additional dependencies
RUN apt-get update && apt-get install -y \
    xorg \
    libx11-dev

RUN if [ "$(uname -m)" = "aarch64" ]; then \
        curl -fsSL https://github.com/bazelbuild/bazelisk/releases/download/v1.20.0/bazelisk-linux-arm64 -o /usr/local/bin/bazel ;\
    else \
        curl -fsSL https://github.com/bazelbuild/bazelisk/releases/download/v1.20.0/bazelisk-linux-amd64 -o /usr/local/bin/bazel ;\
    fi \
    && chmod +x /usr/local/bin/bazel \
    && :

RUN apt-get update && apt-get install -y \
    cmake \
    libuv1-dev \
    libcurl4-openssl-dev \
    libglfw3-dev \
    libpng-dev \
    libsqlite3-dev \
    zlib1g-dev \
    libwebp-dev \
    libjpeg-turbo8-dev

WORKDIR /app

ARG USERNAME=user
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Create docker user with sudo rights as passed in by the build command
# This was modeled on https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user
# On a Mac, USER_GID might already exist, so ignore it if it fails (--force)
RUN groupadd --force --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# using vcxsrv display 0 for windows host machine
ENV DISPLAY=:0

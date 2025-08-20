FROM nvcr.io/nvidia/l4t-tensorrt:r8.6.2-devel AS base

# Set non-interactive frontend for apt
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Taipei

# Update system packages
RUN apt-get update -y && apt-get upgrade -y

# Configure the locale and timezone
RUN apt-get install -y locales tzdata && \
    echo 'en_US.UTF-8 UTF-8' > /etc/locale.gen && \
    locale-gen && \
    update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

# Enable 'universe' repository and install basic tools
RUN apt-get install -y \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    gnupg \
    lsb-release \
    curl \
    wget \
    git \
    && add-apt-repository universe \
    && apt-get update -y

# Install Python and build tools
RUN apt-get install -y \
    python3-venv \
    python3-pip \
    python3-dev \
    sudo \
    build-essential \
    cmake

# Install required dependencies to build Debian packages
RUN apt-get install -y \
    parallel \
    fakeroot \
    debhelper \
    dh-python \
    rsync

# Add ROS 2 APT sources
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key | apt-key add - && \
    echo "deb [arch=$(dpkg --print-architecture)] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" > /etc/apt/sources.list.d/ros2.list && \
    apt-get update -y

# Install ROS 2 Humble base packages
RUN apt-get install -y ros-humble-desktop ros-dev-tools

# Initialize rosdep (run as root in Docker)
RUN rosdep init || true && \
    rosdep update --rosdistro=humble

# Install Ansible first (required by Autoware setup script)
RUN apt-get install -y ansible

# Add NVIDIA L4T APT sources for Jetpack-specific packages
RUN apt-key adv --fetch-key http://repo.download.nvidia.com/jetson/jetson-ota-public.asc && \
    echo "deb https://repo.download.nvidia.com/jetson/common r36.3 main" > /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
    echo "deb https://repo.download.nvidia.com/jetson/t234 r36.3 main" >> /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
    echo "deb https://repo.download.nvidia.com/jetson/ffmpeg r36.3 main" >> /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
    mkdir -p /opt/nvidia/l4t-packages && \
    touch /opt/nvidia/l4t-packages/.nv-l4t-disable-boot-fw-update-in-preinstall && \
    apt-get update -y

# Install NVIDIA L4T DLA compiler (required for Jetpack builds)
RUN apt-get install -o DPkg::Options::="--force-confold" -y nvidia-l4t-dla-compiler

# Clone Autoware repository and run setup
# Note: For 0.45.1, the setup script doesn't support --ros-version flag
# For Jetpack, we skip CUDA drivers as they are included in the base image
RUN git clone --depth 1 --branch 0.45.1 https://github.com/autowarefoundation/autoware.git /tmp/autoware && \
    cd /tmp/autoware && \
    # The 0.45.1 version uses different flags, no-nvidia flag for Jetpack
    ./setup-dev-env.sh -y --no-nvidia --no-cuda-drivers --runtime && \
    rm -rf /tmp/autoware

# Set up ROS environment
ENV ROS_DISTRO=humble
ENV AMENT_PREFIX_PATH=/opt/ros/humble
ENV PYTHONPATH=/opt/ros/humble/lib/python3.10/site-packages
ENV PATH=/opt/ros/humble/bin:$PATH

# Set default process to bash
CMD ["/bin/bash"]

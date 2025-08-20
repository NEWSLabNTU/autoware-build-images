FROM ubuntu:22.04 AS base

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

# Clone Autoware repository and run setup
# Note: For 0.45.1, the setup script doesn't support --ros-version flag
RUN git clone --depth 1 --branch 0.45.1 https://github.com/autowarefoundation/autoware.git /tmp/autoware && \
    cd /tmp/autoware && \
    # The 0.45.1 version uses different flags
    ./setup-dev-env.sh -y --no-nvidia --no-cuda-drivers --runtime && \
    rm -rf /tmp/autoware

# Set up ROS environment
ENV ROS_DISTRO=humble
ENV AMENT_PREFIX_PATH=/opt/ros/humble
ENV PYTHONPATH=/opt/ros/humble/lib/python3.10/site-packages
ENV PATH=/opt/ros/humble/bin:$PATH

# Set default process to bash
CMD ["/bin/bash"]

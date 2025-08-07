FROM ubuntu:22.04 AS base

# Update system packages
RUN apt update -y
RUN apt upgrade -y

# Configure the locale and timezone
RUN apt install -y locales
RUN echo 'en_US.UTF-8 UTF-8' > /etc/locale.gen
RUN locale-gen
RUN update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
RUN ln -snf /usr/share/zoneinfo/Asia/Taipei /etc/localtime && echo Asia/Taipei > /etc/timezone

# Enable 'universe' repository
RUN apt install -y software-properties-common
RUN apt install -y apt-transport-https
RUN add-apt-repository universe

# Install APT packages
RUN apt install -y python3-venv
RUN apt install -y python3-pip
RUN apt install -y sudo
RUN apt install -y openssh-client
RUN apt install -y openssh-server

# Install required dependencies to build Debian packages
RUN apt install -y parallel fakeroot debhelper dh-python rsync

# Install git and other prerequisites for Autoware setup
RUN apt update && apt install -y git curl wget

# Clone Autoware repository and checkout 0.45.1 tag
RUN git clone https://github.com/autowarefoundation/autoware.git /tmp/autoware && \
    cd /tmp/autoware && \
    git checkout 0.45.1

# Run Autoware Ansible setup script
# Note: Using the setup script from the 0.45.1 version
RUN cd /tmp/autoware && \
    ./setup-dev-env.sh -y --download-artifacts --no-cuda-drivers --runtime

# Clean up the cloned Autoware repository
RUN rm -rf /tmp/autoware

# Set default process to bash
CMD ["/bin/bash"]
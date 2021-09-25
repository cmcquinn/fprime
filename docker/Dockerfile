####
# F Prime Base Image:
#
# The F prime base image is an Ubuntu 18.04 image with some basic packages installed to ensure that
# the build tools and needed packages are installed. It also sets-up a virtual environment into
# which the python packages can be installed. This step, however; is deferred to the next layer
# of containers. It ensures the fprime virtual environment is writable by members in the fprime
# group, and owned by an fprime user.
#
# Note: the RPI toolchain is also installed to ensure that the RPI package is ready-to-go.
#
# Build this with the --target fprime-base
####
FROM ubuntu:18.04 AS fprime-base

# Set noninteractive mode for apt installs in Dockerfile
ENV DEBIAN_FRONTEND=noninteractive

# Adding fprime user, group, and password fprime
RUN groupadd fprime && \
    groupmod -g 1001 fprime && \
    useradd -ms /bin/bash -g root -G sudo,fprime -p fprime fprime && \
    usermod -u 1001 fprime

# Installing system components
RUN apt-get update && apt-get install -y --no-install-recommends \
    bash git build-essential cmake time python3 python3-setuptools python3-pip python3-venv valgrind sudo gdb curl gpg

# Install github command line tools
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | gpg --dearmor -o /usr/share/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
    apt update && \
    apt install gh

# Install rpi tools
RUN mkdir -p /opt/ && chown fprime:fprime /opt/ && \
    git clone --quiet https://github.com/raspberrypi/tools.git /opt/rpi/tools

# Install fprime-tools and fprime-gds
RUN pip3 install --no-cache-dir fprime-tools fprime-gds

# Cleanup unneeded apt files
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Allow sudo without password
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> \
    /etc/sudoers

# Change user and group of the virtual environment
# Make fprime user default user
ENV HOST fprime
ENV USER fprime
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8

# Set normal apt frontend so the user can install packages from the command line
ENV DEBIAN_FRONTEND=dialog

USER fprime
ENTRYPOINT ["/bin/bash"]

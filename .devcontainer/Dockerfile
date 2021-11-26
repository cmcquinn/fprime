FROM nasafprime/fprime-base:latest AS fprime-devcontainer

USER root

# Set noninteractive mode for apt installs in Dockerfile
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install --no-install-recommends -y \
    ssh sudo gdb curl gpg wget nano

# Install github command line tools
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | gpg --dearmor -o /usr/share/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
    apt update && \
    apt install gh

# Allow sudo without password
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> \
    /etc/sudoers

USER fprime

# install zsh
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.2/zsh-in-docker.sh)" -- \
    -t agnoster -p git

# Cleanup unneeded apt files
RUN sudo apt-get clean && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set normal apt frontend so the user can install packages from the command line
ENV DEBIAN_FRONTEND=dialog

ENTRYPOINT [ "/usr/bin/zsh" ]
# Deepo base image
FROM cheulyop/deepo:latest

RUN apt-get update \
 && apt-get install -y \
    curl \
    dumb-init \
    htop \
    locales \
    man \
    nano \
    git \
    procps \
    openssh-client \
    sudo \
    vim.tiny \
    lsb-release \
  && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://code-server.dev/install.sh | sh -s -- --version=3.8.0

RUN mkdir -p ~/.local/share/code-server/ && \
    mkdir -p ~/.local/share/code-server/User && echo "{\"extensions.autoCheckUpdates\": false, \"extensions.autoUpdate\": false}" > ~/.local/share/code-server/User/settings.json

RUN wget https://github.com/microsoft/vscode-python/releases/download/2020.10.332292344/ms-python-release.vsix && \
    code-server --install-extension ./ms-python-release.vsix || true

# Add execution permission to entrypoint.sh
RUN chmod +x /usr/bin/entrypoint.sh

# Need port 6006 for tensorboard
EXPOSE 6006 8080

# This way, if someone sets $DOCKER_USER, docker-exec will still work as
# the uid will remain the same. note: only relevant if -u isn't passed to
# docker-run.
USER 1000
ENV USER=coder
WORKDIR /home/coder
ENTRYPOINT ["/usr/bin/entrypoint.sh", "--bind-addr", "0.0.0.0:8080", "."]

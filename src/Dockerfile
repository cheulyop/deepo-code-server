# Deepo base image
FROM cheulyop/deepo:latest

# ==================================================================
# code-server
# ------------------------------------------------------------------
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

# https://wiki.debian.org/Locale#Manually
RUN sed -i "s/# en_US.UTF-8/en_US.UTF-8/" /etc/locale.gen \
  && locale-gen
ENV LANG=en_US.UTF-8

RUN adduser --gecos '' --disabled-password coder && \
  echo "coder ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd

RUN ARCH="$(dpkg --print-architecture)" && \
    curl -fsSL "https://github.com/boxboat/fixuid/releases/download/v0.4.1/fixuid-0.4.1-linux-$ARCH.tar.gz" | tar -C /usr/local/bin -xzf - && \
    chown root:root /usr/local/bin/fixuid && \
    chmod 4755 /usr/local/bin/fixuid && \
    mkdir -p /etc/fixuid && \
    printf "user: coder\ngroup: coder\n" > /etc/fixuid/config.yml

COPY release-packages/code-server*.deb /tmp/
COPY src/entrypoint.sh /usr/bin/entrypoint.sh
RUN dpkg -i /tmp/code-server*$(dpkg --print-architecture).deb && rm /tmp/code-server*.deb

# fix broken python and jupyter extensions
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

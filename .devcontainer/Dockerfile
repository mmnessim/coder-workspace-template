# Use Ubuntu base image
FROM ubuntu:22.04

# Install basic tools
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    git \
    unzip \
    build-essential \
    software-properties-common \
    ca-certificates \
    gnupg \
    lsb-release

# Install Go
ENV GO_VERSION=1.25.1
RUN wget https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz && \
    tar -C /usr/local -xzf go${GO_VERSION}.linux-amd64.tar.gz && \
    rm go${GO_VERSION}.linux-amd64.tar.gz
ENV PATH="/usr/local/go/bin:${PATH}"


# Install Node.js (LTS) and npm
RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - && \
    apt-get install -y nodejs

# Install yarn (optional, for JS/TS projects)
RUN npm install -g yarn

# Install sudo
RUN apt-get update && apt-get install -y sudo

# Install GitHub CLI (gh)
RUN type -p curl >/dev/null || apt-get install -y curl \
    && curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
    && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
    && sudo apt-get update \
    && sudo apt-get install -y gh

# Install JDK 21 (OpenJDK)
RUN sudo apt-get update && sudo apt-get install -y openjdk-21-jdk

# Install Gradle 8.7
RUN wget https://services.gradle.org/distributions/gradle-8.7-bin.zip -O /tmp/gradle.zip \
    && sudo unzip -d /opt/gradle /tmp/gradle.zip \
    && rm /tmp/gradle.zip
ENV GRADLE_HOME=/opt/gradle/gradle-8.7
ENV PATH="${GRADLE_HOME}/bin:${PATH}"

# Install Bun (JavaScript runtime), Clojure tooling, and Swift dependencies
RUN sudo apt-get update && sudo apt-get install -y \
    clojure \
    unzip \
    binutils \
    libc6-dev \
    libcurl4-openssl-dev \
    libedit2 \
    libgcc-9-dev \
    libpython3.8 \
    libsqlite3-0 \
    libstdc++-9-dev \
    libxml2-dev \
    libz3-dev \
    pkg-config \
    tzdata \
    zlib1g-dev \
    && \
    # Install clj-kondo (linter) and babashka (fast scripting)
    curl -sSfL https://raw.githubusercontent.com/clj-kondo/clj-kondo/master/install.sh | bash -s -- -b /usr/local/bin || true && \
    curl -sSfL https://github.com/babashka/babashka/releases/latest/download/babashka-linux-amd64 -o /usr/local/bin/bb || true && \
    chmod +x /usr/local/bin/bb || true && \
    # Install Bun system-wide
    curl -fsSL https://bun.sh/install | bash -s -- --bun-dir /usr/local || true && \
    if [ -f /root/.bun/bin/bun ]; then mv /root/.bun/bin/bun /usr/local/bin/bun || true; fi

# Install Swift
ENV SWIFT_VERSION=5.10.1
RUN wget https://download.swift.org/swift-${SWIFT_VERSION}-release/ubuntu2204/swift-${SWIFT_VERSION}-RELEASE/swift-${SWIFT_VERSION}-RELEASE-ubuntu22.04.tar.gz && \
    tar -xzf swift-${SWIFT_VERSION}-RELEASE-ubuntu22.04.tar.gz && \
    sudo mv swift-${SWIFT_VERSION}-RELEASE-ubuntu22.04 /usr/share/swift && \
    rm swift-${SWIFT_VERSION}-RELEASE-ubuntu22.04.tar.gz
ENV PATH="/usr/share/swift/usr/bin:${PATH}"

# Flutter SDK removed (not required)

# Create coder user (if deploying with Coder, ensure this matches your Coder config)
RUN useradd -m -u 1000 coder && \
    echo "coder ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER coder
WORKDIR /home/coder

# User-level setup: install user Bun
RUN curl -fsSL https://bun.sh/install | bash -s -- --bun-dir /home/coder/.bun && \
    echo 'export PATH="/home/coder/.bun/bin:${PATH}"' >> /home/coder/.profile && \
    /home/coder/.bun/bin/bun --version || true

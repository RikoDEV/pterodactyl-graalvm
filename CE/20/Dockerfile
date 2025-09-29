# ----------------------------------
# Pterodactyl Core Dockerfile
# Environment: Java
# Minimum Panel Version: 1.7.0
# ----------------------------------
  FROM debian:trixie-slim

  ARG TARGETPLATFORM
  ARG JAVA_VERSION=20.0.2

  LABEL maintainer="Tommaso Benatti <tommaso.benatti@outlook.com>"

  ENV DEBIAN_FRONTEND=noninteractive
  
  # Default to UTF-8 file.encoding
  ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'
  
  RUN apt-get update -y \
    && apt-get install -y --no-install-recommends \
        apt-transport-https \
        apt-utils \
        ca-certificates \
        curl \
        fontconfig \
        git \
        gnupg2 \
        iproute2 \
        java-common \
        locales \
        openssl \
        procps \
        software-properties-common \
        sqlite3 \
        tar \
        tzdata \
        wget \
        libfreetype6 \
        build-essential \
        zlib1g-dev \
    && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
    && locale-gen en_US.UTF-8 \
    && case ${TARGETPLATFORM} in \
           "linux/amd64")  ARCH=x64  ;; \
           "linux/arm64")  ARCH=aarch64  ;; \
      esac \
    && curl --retry 3 -Lfso /tmp/graalvm.tar.gz https://github.com/graalvm/graalvm-ce-builds/releases/download/jdk-${JAVA_VERSION}/graalvm-community-jdk-${JAVA_VERSION}_linux-${ARCH}_bin.tar.gz \
    && mkdir -p /opt/java/graalvm \
    && cd /opt/java/graalvm \
    && tar -xf /tmp/graalvm.tar.gz --strip-components=1 \
    && export PATH="/opt/java/graalvm/bin:$PATH" \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/graalvm.tar.gz
  
  ENV JAVA_HOME=/opt/java/graalvm \
      PATH="/opt/java/graalvm/bin:$PATH"
  
  # Step 2 - add pterodactyl stuff
  RUN useradd -d /home/container -m container
  
  USER        container
  ENV         USER=container HOME=/home/container
  
  WORKDIR     /home/container
  
  COPY        ./../entrypoint.sh /entrypoint.sh
  
  CMD         ["/bin/bash", "/entrypoint.sh"]
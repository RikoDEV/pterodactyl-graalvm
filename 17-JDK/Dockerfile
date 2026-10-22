# ----------------------------------
# Pterodactyl Core Dockerfile
# Environment: Java
# Minimum Panel Version: 1.7.0
# ----------------------------------
FROM ubuntu:26.04

ARG TARGETPLATFORM
ARG GRAAL_VERSION=17.0.12
ARG JAVA_VERSION=17

MAINTAINER RikoDEV, <kontakt@riko.dev>

ENV DEBIAN_FRONTEND=noninteractive

# Default to UTF-8 file.encoding
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

RUN apt-get update -y \
  && apt-get install -y --no-install-recommends curl ca-certificates openssl git tar sqlite3 fontconfig tzdata locales iproute2 \
  && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
  && locale-gen en_US.UTF-8 \
  && case ${TARGETPLATFORM} in \
         "linux/amd64")  ARCH=x64  ;; \
         "linux/arm64")  ARCH=aarch64  ;; \
    esac \
  && curl --retry 3 -Lfso /tmp/graalvm.tar.gz https://download.oracle.com/graalvm/${JAVA_VERSION}/archive/graalvm-jdk-${GRAAL_VERSION}_linux-${ARCH}_bin.tar.gz \
  && mkdir -p /opt/java/graalvm \
  && cd /opt/java/graalvm \
  && tar -xf /tmp/graalvm.tar.gz --strip-components=1 \
  && export PATH="/opt/java/graalvm/bin:$PATH" \
  && rm -rf /var/lib/apt/lists/* \
  && rm -rf /tmp/graalvm.tar.gz

ENV JAVA_HOME=/opt/java/graalvm \
    PATH="/opt/java/graalvm/bin:$PATH"

# Mitigate CVE-2026-91403: distrust compromised eMudhra CAs and enforce revocation checks
COPY        ./../cve-2026-91403.sh /tmp/cve-2026-91403.sh
RUN bash /tmp/cve-2026-91403.sh && rm -f /tmp/cve-2026-91403.sh

ENV JAVA_TOOL_OPTIONS="-Dcom.sun.net.ssl.checkRevocation=true -Dcom.sun.security.enableCRLDP=true"

# Step 2 - add pterodactyl stuff
RUN useradd -d /home/container -m container

USER        container
ENV         USER=container HOME=/home/container

WORKDIR     /home/container

COPY        ./../entrypoint.sh /entrypoint.sh

CMD         ["/bin/bash", "/entrypoint.sh"]
FROM ubuntu:xenial

ARG DEBIAN_FRONTEND=noninteractive
ARG APP_VERSION

# NOTE: If you bump the syslinux version here,
#       please also update the README.md.
RUN echo "nameserver 10.0.0.71" > /etc/resolv.conf && \
    apt-get -q -y update && \
    apt-get install -y \
    syslinux=${APP_VERSION} \
    tftpd-hpa && \
    rm -rf /var/lib/apt/lists/*

RUN echo "nameserver 10.0.0.71" > /etc/resolv.conf && \
    mkdir /tftpboot && \
    ln -s /usr/lib/syslinux/modules/bios/* /tftpboot && \
    ln -s /usr/lib/syslinux/modules/ /tftpboot

# Add safe defaults that can be overriden easily.
COPY pxelinux.cfg /tftpboot/pxelinux.cfg/

# Copy pxelinux.0
COPY pxelinux.0 /tftpboot/

# Support clients that use backslash instead of forward slash.
COPY mapfile /tftpboot/

# Do not track further change to /tftpboot.
VOLUME /tftpboot

EXPOSE 69/udp

# Add user tftp only if it doesn't exist already
RUN id -u tftp &>/dev/null || useradd tftp

COPY start /usr/sbin/start
ENTRYPOINT ["/usr/sbin/start"]
CMD ["-L", "--verbose", "-m", "/tftpboot/mapfile", "-u", "tftp", "--secure", "/tftpboot"]

# These go last to preserve build cache.
ARG CI_BUILD_URL
ARG BUILD_DATE
ARG VCS_REF

LABEL \
    io.github.jumanjiman.ci-build-url=$CI_BUILD_URL \
    io.github.jumanjiman.version=$APP_VERSION \
    io.github.jumanjiman.build-date=$BUILD_DATE \
    io.github.jumanjiman.vcs-ref=$VCS_REF \
    io.github.jumanjiman.license="MIT" \
    io.github.jumanjiman.docker.dockerfile="/src/Dockerfile" \
    io.github.jumanjiman.vcs-type="Git" \
    io.github.jumanjiman.vcs-url="https://github.com/jumanjihouse/docker-tftp-hpa.git"

ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base:7.8.3
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

############################### Imported for modbus ################################

# Build deps (Debian)
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      build-essential autoconf automake libtool git pkg-config \
      libusb-1.0-0 libusb-1.0-0-dev ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# libmodbus (USB-RTU branch)
WORKDIR /opt/build
RUN git clone -b rtu_usb https://github.com/networkupstools/libmodbus.git \
 && cd libmodbus && ./autogen.sh \
 && ./configure --with-libusb --enable-static --disable-shared --prefix=/usr/local \
 && make -j"$(nproc)" && make install && ldconfig

# NUT apc_modbus
WORKDIR /opt/build
RUN git clone https://github.com/networkupstools/nut.git \
 && cd nut && ./autogen.sh \
 && ./configure \
      --with-drivers=apc_modbus \
      --with-usb --with-modbus \
      --with-modbus-includes=-I/usr/local/include/modbus \
      --with-modbus-libs="-L/usr/local/lib -lmodbus" \
 && make -j"$(nproc)" \
 && install -m0755 drivers/apc_modbus /usr/sbin/apc_modbus

RUN mkdir -p /etc/nut /var/run/nut
# COPY rootfs /   # keep if you have s6 scripts





############ This part is the same as source#############################


# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Zac Bannon <bannon52@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Zac Bannon <bannon52@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}

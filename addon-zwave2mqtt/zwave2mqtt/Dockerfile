ARG BUILD_FROM=hassioaddons/base:5.0.3
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
# hadolint ignore=DL3003
RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base=0.5-r1 \
        git=2.22.0-r0 \
        linux-headers=4.19.36-r0 \
        npm=10.16.3-r0 \
        openzwave-dev=1.6.962 \
        python2-dev~=2.7 \
        yarn=1.16.0-r0 \
    \
    && apk add --no-cache \
        eudev=3.2.8-r0 \
        libusb=1.0.22-r0 \
        lua-resty-http=0.13-r0 \
        nginx-mod-http-lua=1.16.1-r1 \
        nginx=1.16.1-r1 \
        nodejs=10.16.3-r0 \
        openssl=1.1.1d-r0 \
        openzwave=1.6.962 \
    \
    && curl -J -L -o /tmp/openzwave-db.tar.gz \
        "http://old.openzwave.com/downloads/openzwave-1.6.962.tar.gz" \
    && mkdir /tmp/db \
    && tar zxvf \
        /tmp/openzwave-db.tar.gz \
        --strip 1 -C /tmp/db \
    \
    && mkdir /usr/etc \
    && mv /tmp/db/config /usr/etc/openzwave \
    \
    && curl -J -L -o /tmp/zwave2mqtt.tar.gz \
        "https://github.com/OpenZWave/Zwave2Mqtt/archive/v2.0.6.tar.gz" \
    && tar zxvf \
        /tmp/zwave2mqtt.tar.gz \
        --strip 1 -C /opt \
    \
    && cd /opt \
    && npm config set unsafe-perm \
    && npm install \
    && npm run build \
    && npm prune --production \
    \
    && yarn global add modclean \
    && modclean \
        --path /opt \
        --no-progress \
        --keep-empty \
        --run \
    && yarn global remove modclean \
    && yarn cache clean \
    \
    && apk del --purge .build-dependencies \
    \
    && rm -f -r \
        /etc/nginx \
        /etc/openzwave \
        /root/.node-gyp \
        /root/.npm \
        /tmp/.[!.]* \
        /tmp/* \
        /usr/lib/node_modules \
        /usr/local/share/.cache

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="Z-Wave to MQTT" \
    io.hass.description="Fully configurable Z-Wave to MQTT gateway and control panel." \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.label-schema.description="Fully configurable Z-Wave to MQTT gateway and control panel." \
    org.label-schema.build-date=${BUILD_DATE} \
    org.label-schema.name="Z-Wave to MQTT" \
    org.label-schema.schema-version="1.0" \
    org.label-schema.url="https://community.home-assistant.io/t/community-hass-io-add-on-z-wave-to-mqtt/115899?u=frenck" \
    org.label-schema.usage="https://github.com/hassio-addons/addon-zwave2mqtt/tree/master/README.md" \
    org.label-schema.vcs-ref=${BUILD_REF} \
    org.label-schema.vcs-url="https://github.com/hassio-addons/addon-zwave2mqtt" \
    org.label-schema.vendor="Community Hass.io Add-ons"

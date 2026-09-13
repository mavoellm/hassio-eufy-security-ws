ARG BUILD_FROM
FROM $BUILD_FROM

ARG EUFY_SECURITY_WS_VERSION

WORKDIR /usr/src/app

RUN \
    set -x \
    && apk add --no-cache \
        nodejs \
        npm \
        jq \
        git

COPY package.json /usr/src/app/

# Install patched eufy-security-client from our fork
# Build patched eufy-security-client into the image.
# This avoids rebuilding it on every add-on restart.
RUN \
    set -x \
    && git clone --depth 1 \
        --branch ha-node20-code200-fix \
        https://github.com/mavoellm/eufy-security-client.git \
        /tmp/eufy-security-client \
    && cd /tmp/eufy-security-client \
    && npm ci \
    && npm run build -y \
    && npm pack \
    && mv eufy-security-client-*.tgz /usr/src/app/eufy-security-client.tgz \
    && cd /usr/src/app \
    && npm pkg set dependencies.eufy-security-ws="${EUFY_SECURITY_WS_VERSION}" \
    && npm pkg set overrides.eufy-security-client="file:eufy-security-client.tgz" \
    && npm install --force \
    && rm -rf /tmp/eufy-security-client \
    && npm cache clean --force

COPY run.sh /
RUN chmod a+x /run.sh

WORKDIR /data

CMD [ "/run.sh" ]

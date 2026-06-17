ARG BUILD_FROM
FROM $BUILD_FROM

ARG EUFY_SECURITY_WS_VERSION

WORKDIR /usr/src/app
RUN \
    set -x \
    # The Alpine 3.23 base image (see build.yaml) ships Node.js 24 in its main
    # repository, so nodejs/npm install directly with no edge-repo workarounds.
    && apk add --no-cache \
        jq \
        nodejs \
        npm \
    && node --version \
    # Fail the build if the base ever regresses below Node 24.
    && case "$(node --version)" in v24.*) ;; *) echo "Expected Node 24, got $(node --version)" >&2; exit 1 ;; esac \
    && npm install --force \
    "eufy-security-ws@${EUFY_SECURITY_WS_VERSION}" \
    # Patch eufy-security-client in-place: overwrite the two compiled JS files
    # that fix the OUTDOOR_PT_CAMERA enabled-state sync regression (issue #916).
    # Using wget on the raw compiled files avoids needing git or a full reinstall.
    && PATCH_BASE="https://raw.githubusercontent.com/stephanevalk/eufy-security-client/8543f745aa282e71599abda1ce951616bffc09a5/build/http" \
    && wget -q -O node_modules/eufy-security-client/build/http/types.js  "${PATCH_BASE}/types.js" \
    && wget -q -O node_modules/eufy-security-client/build/http/station.js "${PATCH_BASE}/station.js"

COPY run.sh /
RUN chmod a+x /run.sh
WORKDIR /data

CMD [ "/run.sh" ]
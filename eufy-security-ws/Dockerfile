ARG BUILD_FROM
FROM $BUILD_FROM

ARG EUFY_SECURITY_WS_VERSION

WORKDIR /usr/src/app
RUN \
    set -x \
    # The Alpine 3.23 base image (see build.yaml) ships Node.js 24 in its main
    # repository, so nodejs/npm install directly with no edge-repo workarounds.
    && apk add --no-cache \
        git \
        jq \
        nodejs \
        npm \
    && node --version \
    # Fail the build if the base ever regresses below Node 24.
    && case "$(node --version)" in v24.*) ;; *) echo "Expected Node 24, got $(node --version)" >&2; exit 1 ;; esac \
    && npm install --force \
    "eufy-security-ws@${EUFY_SECURITY_WS_VERSION}" \
    # Override eufy-security-client with a patched fork that fixes the
    # OUTDOOR_PT_CAMERA enabled-state sync regression (issue #916).
    && npm install --force \
    "eufy-security-client@github:stephanevalk/eufy-security-client#fix/outdoor-pt-cloud-sync"

COPY run.sh /
RUN chmod a+x /run.sh
WORKDIR /data

CMD [ "/run.sh" ]
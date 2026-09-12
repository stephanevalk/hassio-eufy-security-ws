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
    "eufy-security-ws@${EUFY_SECURITY_WS_VERSION}"

# Patch eufy-security-client in-place: overwrite the compiled JS file that fixes
# the OUTDOOR_PT_CAMERA (S340) enabled-state sync regression (issue #916).
# The patched file is bundled in this repo (patch/) and copied in directly,
# so the build has no dependency on GitHub raw / CDN availability.
# Rebuild patch/station.js from the matching client release whenever
# EUFY_SECURITY_WS_VERSION changes -- it is a full copy, not a delta.
COPY patch/station.js node_modules/eufy-security-client/build/http/station.js

COPY run.sh /
RUN chmod a+x /run.sh
WORKDIR /data

CMD [ "/run.sh" ]
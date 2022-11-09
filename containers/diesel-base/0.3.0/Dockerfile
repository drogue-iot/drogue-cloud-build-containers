FROM registry.access.redhat.com/ubi9-minimal

LABEL org.opencontainers.image.source="https://github.com/drogue-iot/drogue-cloud-build-containers"

RUN microdnf install -y libpq xz

RUN true \
    && curl -sSL https://raw.githubusercontent.com/drogue-iot/drogue-cloud-build-tools/main/binaries/$(uname -p)/diesel/2.0.1/diesel.xz -o diesel.xz \
    && unxz diesel.xz \
    && install -m 0555 diesel /usr/local/bin/ \
    && rm diesel

ENTRYPOINT ["/usr/local/bin/diesel"]


FROM registry.access.redhat.com/ubi8/ubi:latest as builder

RUN dnf -y update
RUN dnf -y install curl openssl-devel gcc gcc-c++ make cyrus-sasl-devel cmake libpq-devel

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y

ENV PATH "$PATH:/root/.cargo/bin"

RUN cargo install diesel_cli --no-default-features --features postgres

FROM registry.access.redhat.com/ubi8-minimal

LABEL org.opencontainers.image.source="https://github.com/drogue-iot/drogue-cloud-build-containers"

RUN microdnf install -y libpq
COPY --from=builder /root/.cargo/bin/diesel /usr/local/bin/

ENTRYPOINT ["/usr/local/bin/diesel"]

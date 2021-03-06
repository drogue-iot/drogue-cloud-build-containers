# Build

FROM registry.access.redhat.com/ubi8/ubi:latest

LABEL org.opencontainers.image.source="https://github.com/drogue-iot/drogue-cloud-build-containers"

RUN dnf -y update
RUN dnf -y install curl openssl openssl-devel npm gcc gcc-c++ make cyrus-sasl-devel cmake libpq-devel

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y --default-toolchain none

ENV PATH "$PATH:/root/.cargo/bin"

RUN rustup toolchain install 1.52
RUN rustup target add wasm32-unknown-unknown

RUN \
     curl -sSL https://github.com/rustwasm/wasm-pack/releases/download/v0.9.1/wasm-pack-v0.9.1-x86_64-unknown-linux-musl.tar.gz -o wasm-pack.tar.gz \
  && tar --strip-components=1 -xvzf wasm-pack.tar.gz '*/wasm-pack' \
  && rm -Rf wasm-pack.tar.gz \
  && cp wasm-pack /root/.cargo/bin/ && rm wasm-pack

RUN \
     curl -sSL https://github.com/rustwasm/wasm-bindgen/releases/download/0.2.73/wasm-bindgen-0.2.73-x86_64-unknown-linux-musl.tar.gz -o wasm-bindgen.tar.gz \
  && tar --strip-components=1 -xvzf wasm-bindgen.tar.gz '*/wasm-bindgen' \
  && rm -Rf wasm-bindgen.tar.gz \
  && cp wasm-bindgen /root/.cargo/bin/ && rm wasm-bindgen

RUN \
    curl -sSL https://github.com/WebAssembly/binaryen/releases/download/version_97/binaryen-version_97-x86_64-linux.tar.gz -o binaryen.tar.gz \
 && tar --strip-components=2 -xzvf binaryen.tar.gz '*/wasm-opt' \
 && rm -Rf binaryen.tar.gz \
 && cp wasm-opt /root/.cargo/bin/ && rm wasm-opt

ENV WASM_PACK_PATH "/root/.cargo/bin/wasm-pack"

RUN mkdir -p /usr/src
VOLUME /usr/src/

ENV CARGO_HOME "/usr/src/.cargo-container-home"

RUN dnf install -y https://download.docker.com/linux/centos/8/x86_64/stable/Packages/docker-ce-cli-20.10.2-3.el8.x86_64.rpm

RUN dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
RUN dnf install -y postgresql12
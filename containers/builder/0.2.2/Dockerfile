# Build

FROM registry.access.redhat.com/ubi9/ubi:latest

LABEL org.opencontainers.image.source="https://github.com/drogue-iot/drogue-cloud-build-containers"

RUN dnf -y update
RUN dnf -y install curl-minimal openssl openssl-devel gcc gcc-c++ make cyrus-sasl-devel cmake libpq-devel perl xz postgresql
RUN dnf -y install nodejs

ENV RUSTUP_HOME=/opt/rust
ENV CARGO_HOME=/opt/rust

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y --default-toolchain none

ENV PATH "$PATH:$CARGO_HOME/bin"

# This needs to be aligned with the rust-toolchain.toml file in drogue-iot/drogue-cloud
RUN rustup toolchain install 1.62.0
RUN rustup target add wasm32-unknown-unknown
RUN rustup target add x86_64-unknown-linux-gnu
RUN rustup target add aarch64-unknown-linux-gnu
RUN rustup target add x86_64-apple-darwin
RUN rustup target add x86_64-pc-windows-msvc
RUN rustup component add rust-src

# The following tools need to be aligned with the console-frontend/Trunk.toml file
RUN true \
    && curl -sSL https://github.com/rustwasm/wasm-pack/releases/download/v0.10.3/wasm-pack-v0.10.3-$(uname -p)-unknown-linux-musl.tar.gz -o wasm-pack.tar.gz \
    && tar --strip-components=1 -xvzf wasm-pack.tar.gz '*/wasm-pack' \
    && rm wasm-pack.tar.gz \
    && cp wasm-pack /usr/local/bin/ && rm wasm-pack

RUN true \
    && curl -sSL https://raw.githubusercontent.com/drogue-iot/drogue-cloud-build-tools/main/binaries/$(uname -p)/wasm-bindgen/0.2.81/wasm-bindgen.xz -o wasm-bindgen.xz \
    && unxz wasm-bindgen.xz \
    && install -m 0555 wasm-bindgen /usr/local/bin/ \
    && rm wasm-bindgen

RUN \
    case "$(uname -p)" in \
        aarch64) \
            ;; \
        *) \
               curl -sSL https://github.com/WebAssembly/binaryen/releases/download/version_109/binaryen-version_109-x86_64-linux.tar.gz -o binaryen.tar.gz \
            && tar --strip-components=2 -xzvf binaryen.tar.gz '*/wasm-opt' \
            && rm binaryen.tar.gz \
            && cp wasm-opt /usr/local/bin/ && rm wasm-opt \
            ;; \
    esac

RUN npm install -g sass@1.52.3 && sass --version

# We use our own binary due to aarch64 and an issue with GLIBC_2.29
RUN true \
    && curl -sSL https://raw.githubusercontent.com/drogue-iot/drogue-cloud-build-tools/main/binaries/$(uname -p)/trunk/0.15.0/trunk.xz -o trunk.xz \
    && unxz trunk.xz \
    && install -m 0555 trunk /usr/local/bin/ \
    && rm trunk
ENV XDG_CACHE_HOME=/cache
RUN install -m0777 -d $XDG_CACHE_HOME

# Required for some testing scenarios
RUN dnf install -y https://download.docker.com/linux/centos/9/$(uname -p)/stable/Packages/docker-ce-cli-20.10.17-3.el9.$(uname -p).rpm

# Set the cache directory after installing tools using npm, and make it accessible
ENV npm_config_cache=/opt/npm
RUN mkdir $npm_config_cache && chmod a+rwx $npm_config_cache

# This is used by the wasm-pack-plugin for webpack
ENV WASM_PACK_PATH "/usr/local/bin/wasm-pack"

RUN install -m 0777 -d /usr/src
VOLUME /usr/src/

ENV CARGO_HOME "/usr/src/.cargo-container-home"

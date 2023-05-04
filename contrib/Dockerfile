FROM debian:bullseye

ENV PROJECT="alsa-test"
ENV HOST_ARCH="aarch64"
ENV BUILD_ARCH="x86_64"
ENV RUSTUP_INIT_URL="https://static.rust-lang.org/rustup/dist/${BUILD_ARCH}-unknown-linux-gnu/rustup-init"

# rust installation
RUN set -eux; \
    apt-get update \
    && apt-get dist-upgrade -y \
    && apt-get install -y wget \
    && wget "$RUSTUP_INIT_URL" \
    && chmod +x ./rustup-init \
    && ./rustup-init -y --no-modify-path --default-toolchain "stable-${HOST_ARCH}-unknown-linux-gnu" --default-host "${BUILD_ARCH}-unknown-linux-gnu" \
    && apt-get remove --purge -y wget \
    && rm ./rustup-init

# dependencies for compiling
RUN set -eux; \
    dpkg --add-architecture arm64 \
    && apt-get update \
    && apt-get install -y \
        build-essential \
        pkg-config \
        gcc-aarch64-linux-gnu \
        libasound2:arm64 \
        libasound2-dev:arm64

COPY .. /data/${PROJECT}
ENV PATH="/root/.cargo/bin:${PATH}"

WORKDIR "/data/${PROJECT}"
# fetch all dependencies to avoid redownloading
#   1. toolchains in rust-toolchain.toml
#   2. crates.io registry
#   3. dependencies
# on every run
RUN set -eux; \
    cargo fetch

CMD ["cargo", "build", "--release"]

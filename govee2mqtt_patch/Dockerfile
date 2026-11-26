ARG BUILD_FROM
FROM rust:1-bookworm AS builder
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates git cmake clang libclang-dev pkg-config libssl-dev \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /src
# Exact reviewed commit of upstream PR #739, not a moving branch.
RUN git init && git remote add origin https://github.com/wez/govee2mqtt.git \
    && git fetch --depth=1 origin 5e60a5568bcc68eca774571c6644a1bebe7224fa \
    && git checkout --detach FETCH_HEAD \
    && test "$(git rev-parse HEAD)" = 5e60a5568bcc68eca774571c6644a1bebe7224fa
RUN cargo build --locked --release --bin govee

FROM ${BUILD_FROM}
WORKDIR /app
COPY --from=builder /src/target/release/govee /app/govee
COPY --from=builder /src/assets /app/assets/
COPY --from=builder /src/AmazonRootCA1.pem /app/
COPY --from=builder /src/LICENSE.md /app/LICENSE.md
COPY --from=builder /src/addon/run.sh /run.sh
RUN chmod 0755 /run.sh && /app/govee --help >/dev/null
CMD ["/run.sh"]

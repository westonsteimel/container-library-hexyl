ARG GIT_TAG="v0.8.0"
ARG HEXYL_VERSION="${GIT_TAG}"

FROM westonsteimel/rust-static-builder:stable as builder

ARG GIT_TAG

RUN git clone --depth 1 --branch "${GIT_TAG}" https://github.com/sharkdp/hexyl /build/hexyl \
    && cd /build/hexyl \
    && /home/builder/build.sh

FROM alpine:latest as config

RUN addgroup hexyl \
    && adduser -G hexyl -s /bin/sh -D hexyl

FROM scratch

ARG HEXYL_VERSION

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/hexyl/target/x86_64-unknown-linux-musl/release/hexyl /usr/local/bin/hexyl

USER hexyl

ENV TERM xterm-256color

ENTRYPOINT ["/usr/local/bin/hexyl"]
CMD ["--help"]

LABEL org.opencontainers.image.title="hexyl" \
    org.opencontainers.image.description="hexyl in Docker" \
    org.opencontainers.image.version="${HEXYL_VERSION}" \
    version="${HEXYL_VERSION}"


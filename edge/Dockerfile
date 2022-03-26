ARG VERSION="master"
ARG REVISION="7da04c6bff1599b028a982d0dde67760c89279a3"

FROM ghcr.io/westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "${VERSION}" https://github.com/sharkdp/hexyl /build/hexyl \
    && cd /build/hexyl \
    && git reset --hard "${REVISION}" \
    && NOCOMPRESS=1 /home/builder/build.sh 

FROM alpine:edge as config

RUN addgroup hexyl \
    && adduser -G hexyl -s /bin/sh -D hexyl

FROM scratch

ARG VERSION
ARG REVISION
ENV HEXYL_VERSION="${VERSION}" \
    HEXYL_REVISION="${REVISION}" \
    TERM="xterm-256color"


COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/artifacts/hexyl /usr/local/bin/hexyl

USER hexyl

ENTRYPOINT ["/usr/local/bin/hexyl"]
CMD ["--help"]

LABEL org.opencontainers.image.title="hexyl" \
    org.opencontainers.image.description="hexyl in Docker" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.version="${VERSION}"


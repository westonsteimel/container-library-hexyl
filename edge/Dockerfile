ARG VERSION="master"
ARG REVISION="7c9f7e1bd02403c0b71625d01372925a4598e48c"

FROM ghcr.io/westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "${VERSION}" https://github.com/sharkdp/hexyl /build/hexyl \
    && cd /build/hexyl \
    && git reset --hard "${REVISION}" \
    && mkdir /build/output \
    && NOCOMPRESS=1 /home/builder/build.sh \
    && cp /build/hexyl/target/${TARGET}/release/hexyl /build/output/

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
COPY --from=builder /build/output/hexyl /usr/local/bin/hexyl

USER hexyl

ENTRYPOINT ["/usr/local/bin/hexyl"]
CMD ["--help"]

LABEL org.opencontainers.image.title="hexyl" \
    org.opencontainers.image.description="hexyl in Docker" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.version="${VERSION}"


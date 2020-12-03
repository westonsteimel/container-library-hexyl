ARG SOURCE
ARG REVISION
ARG CREATED
ARG VERSION
ARG GIT_REF="${VERSION}"

FROM westonsteimel/rust-static-builder:nightly as builder

ARG GIT_REF

RUN git clone --depth 1 --branch "${GIT_REF}" https://github.com/sharkdp/hexyl /build/hexyl \
    && cd /build/hexyl \
    && mkdir output \
    && /home/builder/build.sh \
    && cp /build/hexyl/target/${TARGET}/release/hexyl /build/hexyl/output/
    
FROM alpine:edge as config

RUN addgroup hexyl \
    && adduser -G hexyl -s /bin/sh -D hexyl

FROM scratch

ARG VERSION
ARG SOURCE
ARG REVISION
ARG CREATED

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/hexyl/output/ /usr/local/bin/hexyl

USER hexyl

ENV TERM xterm-256color

ENTRYPOINT ["/usr/local/bin/hexyl"]
CMD ["--help"]

LABEL org.opencontainers.image.title="hexyl" \
    org.opencontainers.image.description="hexyl in Docker" \
    org.opencontainers.image.source="${SOURCE}" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.created="${CREATED}" \
    org.opencontainers.image.version="${VERSION}"


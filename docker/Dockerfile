ARG GO
FROM golang:${GO}-bookworm AS builder
ARG VERSION
WORKDIR /src
COPY src .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -trimpath -ldflags="-s -w -X main.GitCommit=${VERSION}" -o /out/tonutils-storage ./cli

FROM debian:bookworm-slim
RUN apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates && \
    rm -rf /var/lib/apt/lists/*
COPY --from=builder /out/tonutils-storage /usr/local/bin/tonutils-storage
COPY docker/entrypoint.sh /usr/local/bin/tonutils-storage-entrypoint.sh
RUN mkdir -p /data/db /bags
EXPOSE 13474 47431/udp
ENTRYPOINT ["/usr/local/bin/tonutils-storage-entrypoint.sh"]
CMD ["--daemon", "--db", "/data/db", "--api", "0.0.0.0:13474"]

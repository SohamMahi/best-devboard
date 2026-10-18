# Single-stage: build and run the Go binary from the same golang image.
# (No multi-stage yet — slimming the final image comes later in the course.)
FROM golang:1.25.14-alpine3.22 AS builder

WORKDIR /src

COPY go.mod go.sum ./

RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    go mod download

COPY . .

RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=$(go env GOARCH) \
    go build \
    -trimpath \
    -buildvcs=false \
    -ldflags="-s -w" \
    -o /out/devboard-backend .

FROM scratch AS runtime

COPY --from=builder \
    /etc/ssl/certs/ca-certificates.crt \
    /etc/ssl/certs/ca-certificates.crt

COPY --from=builder \
    --chown=10001:10001 \
    /out/devboard-backend \
    /usr/local/bin/devboard-backend

WORKDIR /app

USER 10001:10001

EXPOSE 8080

ENTRYPOINT ["/usr/local/bin/devboard-backend"]





# Single-stage: build and run the Go binary from the same golang image.
# (No multi-stage yet — slimming the final image comes later in the course.)
FROM golang:1.22-alpine3.20 AS builder

WORKDIR /src

RUN apk add --no-cache ca-certificates=20241121-r1

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

FROM alpine:3.20 AS runtime

RUN apk add --no-cache ca-certificates=20241121-r1 \
    && addgroup -S -g 10001 app \
    && adduser -S -D -H -u 10001 -G app app

COPY --from=builder --chown=app:app \
    /out/devboard-backend \
    /usr/local/bin/devboard-backend

WORKDIR /app

USER 10001:10001

EXPOSE 8080

ENTRYPOINT ["/usr/local/bin/devboard-backend"]



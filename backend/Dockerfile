# Single-stage: build and run the Go binary from the same golang image.
# (No multi-stage yet — slimming the final image comes later in the course.)
FROM golang:1.22-alpine AS builder

WORKDIR /src

RUN apk add --no-cache ca-certificates

# Cache dependency downloads.
COPY go.mod go.sum ./
RUN go mod download

# Copy application source and build a static, stripped binary.
COPY . .

RUN CGO_ENABLED=0 \
    GOOS=linux \
    go build \
    -trimpath \
    -buildvcs=false \
    -ldflags="-s -w" \
    -o /out/devboard-backend .

FROM alpine:3.20 AS runtime

RUN apk add --no-cache ca-certificates \
    && addgroup -S -g 10001 app \
    && adduser -S -D -H -u 10001 -G app app

COPY --from=builder --chown=app:app \
    /out/devboard-backend \
    /usr/local/bin/devboard-backend

WORKDIR /app

USER 10001:10001

EXPOSE 8080

ENTRYPOINT ["/usr/local/bin/devboard-backend"]


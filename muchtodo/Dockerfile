# ---------- Build Stage ----------
FROM golang:1.25-alpine AS builder

WORKDIR /app

RUN apk add --no-cache git

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN go install github.com/swaggo/swag/cmd/swag@latest
RUN swag init -g ./cmd/api/main.go -o ./docs

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -o muchtodo ./cmd/api/main.go


# ---------- Runtime Stage ----------
FROM alpine:3.18


WORKDIR /app

RUN adduser -D appuser
RUN apk add --no-cache ca-certificates

COPY --from=builder /app/muchtodo .
COPY --from=builder /app/docs ./docs

USER appuser

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s \
  CMD wget -qO- http://localhost:8080/health || exit 1

CMD ["./muchtodo"]

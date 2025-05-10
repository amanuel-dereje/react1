FROM golang:1.23 AS build

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -o api ./cmd/api/

FROM alpine:3.20 AS prod
WORKDIR /app

COPY --from=build /etc/ssl/certs /etc/ssl/certs
COPY --from=build /app/api .

EXPOSE ${PORT}
CMD ["./api"]

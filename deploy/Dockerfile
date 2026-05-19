FROM golang:1.21-alpine AS build
WORKDIR /src
COPY . .
RUN go build -o /out/nats-server .

FROM alpine:3.18
RUN apk add --no-cache openssl
# Generate inter-broker mTLS key (RSA-2048, SHA-256)
RUN openssl req -x509 -newkey rsa:2048 -keyout /etc/nats/broker.key -out /etc/nats/broker.crt \
    -days 365 -nodes -sha256 -subj "/CN=event-bus.meridian-federal.internal"
COPY --from=build /out/nats-server /nats-server
ENV TLS_PROTOCOL="TLSv1.2"
ENV CLIENT_AUTH_ALGORITHM="RSA-2048"
EXPOSE 4443 4222
ENTRYPOINT ["/nats-server"]

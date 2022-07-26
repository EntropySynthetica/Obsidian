Source https://dev.to/youngyoshie/dockerfile-for-go-4jjp?utm_source=dormosheio&utm_campaign=dormosheio

```Dockerfile
FROM golang:1.18beta1-bullseye as builder

WORKDIR /build

COPY go.mod .
COPY go.sum .
COPY vendor .
COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 GOAMD64=v3 go build -o ./app main.go

FROM gcr.io/distroless/base-debian11

COPY --from=builder /build/app /app

ENTRYPOINT ["/app"]
```

Dockerfile I have used for builds

```Dockerfile
FROM golang:1.18.4-bullseye as builder
WORKDIR /build
COPY go.mod ./
RUN go mod download
COPY . ./
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o ./echo-http main.go


FROM alpine:3.16.1
RUN apk --no-cache add ca-certificates
COPY --from=builder /build/echo-http /echo-http

CMD ["/echo-http"]
```
Source https://dev.to/youngyoshie/dockerfile-for-go-4jjp?utm_source=dormosheio&utm_campaign=dormosheio

```
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
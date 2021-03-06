FROM golang:1.9.4 as build

WORKDIR /go/src/github.com/dailyiot/diot_device/gateway

COPY vendor         vendor
COPY server.go      .

# Run a gofmt and exclude all vendored code.
RUN test -z "$(gofmt -l $(find . -type f -name '*.go' -not -path "./vendor/*"))" \
    && go test $(go list ./... | grep -v integration | grep -v /vendor/ | grep -v /template/) -cover \
    && CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o gateway .

FROM alpine:3.7

RUN addgroup -S app \
    && adduser -S -g app app

WORKDIR /home/app

EXPOSE 6107

COPY --from=build /go/src/github.com/dailyiot/diot_device/gateway/gateway .

RUN chown -R app:app ./

USER app

CMD ["./gateway"]

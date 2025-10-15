# syntax=docker/dockerfile:experimental
FROM golang:1.19 as builder
ARG TARGETOS
ARG TARGETARCH
ARG APP_BUILD_INFO
WORKDIR /go/src/app
COPY . .
RUN export GOPATH=/go
RUN go get -d -v .
RUN gofmt -s -w ./
RUN echo $(git describe --tags --abbrev=0)-$(echo -n ${APP_BUILD_INFO}|cut -c1-7)-${TARGETARCH}
RUN CGO_ENABLED=0 GOOS=${TARGETOS} GOARCH=${TARGETARCH}  \
    go build -v -o kbot\ 
    -ldflags "-X="github.com/den-vasyliev/kbot/cmd.appVersion=$(git describe --tags --abbrev=0)-$(echo -n ${APP_BUILD_INFO}|cut -c1-7)-${TARGETARCH}

FROM scratch AS bin
WORKDIR /
COPY --from=builder /go/src/app/kbot .
COPY --from=alpine:latest /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
EXPOSE 8080
ENTRYPOINT ["./kbot", "server"]

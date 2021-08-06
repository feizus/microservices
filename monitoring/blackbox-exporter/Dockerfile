FROM golang:1.11 as golang

ARG VERSION=0.14.0

WORKDIR /go/src/github.com/blackbox_exporter

RUN git clone https://github.com/prometheus/blackbox_exporter.git . && \
    git checkout tags/v"${VERSION}" && \
    make build

FROM quay.io/prometheus/busybox:latest

COPY --from=golang /go/src/github.com/blackbox_exporter/blackbox_exporter  /bin/blackbox_exporter
COPY blackbox.yml       /etc/blackbox_exporter/config.yml

EXPOSE      9115
ENTRYPOINT  [ "/bin/blackbox_exporter" ]
CMD         [ "--config.file=/etc/blackbox_exporter/config.yml" ]


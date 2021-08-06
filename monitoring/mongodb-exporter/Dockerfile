FROM golang:1.11 as golang

ARG VERSION=0.8.0

WORKDIR /go/src/github.com/percona/mongodb_exporter

RUN git clone https://github.com/percona/mongodb_exporter.git . && \
    git checkout tags/v"${VERSION}" && \
    make build

FROM quay.io/prometheus/busybox:latest

COPY --from=golang /go/src/github.com/percona/mongodb_exporter/bin/mongodb_exporter /bin/mongodb_exporter

EXPOSE 9216

ENTRYPOINT [ "/bin/mongodb_exporter" ]

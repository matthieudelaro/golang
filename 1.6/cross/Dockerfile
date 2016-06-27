FROM buildpack-deps:jessie-scm

# gcc for cgo
RUN apt-get update && apt-get install -y --no-install-recommends \
        g++ \
        gcc \
        libc6-dev \
        make \
    && rm -rf /var/lib/apt/lists/*

ENV GOLANG_VERSION 1.6.2
ENV GOLANG_DOWNLOAD_URL https://golang.org/dl/go$GOLANG_VERSION.linux-amd64.tar.gz
ENV GOLANG_DOWNLOAD_SHA256 e40c36ae71756198478624ed1bb4ce17597b3c19d243f3f0899bb5740d56212a

ENV GOPATH /go
WORKDIR $GOPATH
COPY go-wrapper /usr/local/bin/

# Go build systems relies on Go.
# So:
# 1 - download Go (called bootstrap)
# 2 - download sources of Go
# 3 - compile sources
# 4 - remove useless files
RUN curl -fsSL "$GOLANG_DOWNLOAD_URL" -o golang.tar.gz \
    && echo "$GOLANG_DOWNLOAD_SHA256  golang.tar.gz" | sha256sum -c - \
    && tar -C /usr/local -xzf golang.tar.gz \
    && rm golang.tar.gz \
    && mv /usr/local/go /usr/local/go_bootstrap \
    && mkdir -p "$GOPATH/src" "$GOPATH/bin" && chmod -R 777 "$GOPATH" \
    && git clone https://go.googlesource.com/go /usr/local/go \
    && cd /usr/local/go \
    && git checkout "go$GOLANG_VERSION" \
    && export GOROOT_BOOTSTRAP=/usr/local/go_bootstrap \
    && cd /usr/local/go/src \
    && env GOOS=linux GOARCH=amd64 ./make.bash \
    && env GOOS=darwin GOARCH=amd64 ./make.bash \
    && env GOOS=windows GOARCH=amd64 ./make.bash \
    && rm -rf /usr/local/go/.git \
    && rm -rf /usr/local/go_bootstrap

ENV PATH $GOPATH/bin:/usr/local/go/bin:$PATH

RUN go get -u github.com/kardianos/govendor

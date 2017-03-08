# Amazon ECR credential-helper
# @see https://github.com/awslabs/amazon-ecr-credential-helper

FROM alpine:3.5

# Commit at Feb 25, 2017, 03:20 PM
ENV APP_VERSION=589f7070767646f2b220504930f803accb1e88a7 \
    REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
    METHOD=get

RUN apk add --no-cache ca-certificates
RUN apk --no-cache add --virtual build-dependencies bash gcc g++ musl-dev openssl go git \

    # Install go 1.8
    && GOLANG_VERSION=1.8 \
    && GOLANG_SRC_URL=https://golang.org/dl/go$GOLANG_VERSION.src.tar.gz \
    && GOLANG_SRC_SHA256=406865f587b44be7092f206d73fc1de252600b79b3cacc587b74b5ef5c623596 \
    && export GOROOT_BOOTSTRAP="$(go env GOROOT)" \
    && wget -q "$GOLANG_SRC_URL" -O golang.tar.gz \
    && echo "$GOLANG_SRC_SHA256  golang.tar.gz" | sha256sum -c - \
    && tar -C /usr/local -xzf golang.tar.gz \
    && cd /usr/local/go/src \
    && wget -q https://raw.githubusercontent.com/docker-library/golang/master/1.8/alpine/no-pic.patch \
    && patch -p2 -i no-pic.patch \
    && export GOPATH=/go \
    && mkdir -p /go/src \
    && ./make.bash \
    && chmod -R 777 /go \

    # Build Amazon ECR credential-helper
    && APP_REPO=github.com/awslabs/amazon-ecr-credential-helper \
    && mkdir -p $GOPATH/src/$APP_REPO \
    && git clone https://$APP_REPO $GOPATH/src/$APP_REPO \
    && cd $GOPATH/src/$APP_REPO \
    && git checkout $APP_VERSION \
    && GOOS=linux CGO_ENABLED=0 go build -installsuffix cgo -a -ldflags '-s' \
       -o /usr/bin/docker-credential-ecr-login ./ecr-login/cmd/ \

    # Clean up
    && apk del --purge -r build-dependencies \
    && rm -rf /usr/local/go /usr/lib/go /go /golang.tar.gz /var/cache/* /var/lib/apk*

ENTRYPOINT ["/bin/sh"]
CMD ["-c", "echo $REGISTRY | /usr/bin/docker-credential-ecr-login $METHOD"]

# Amazon ECR credential-helper
# @see https://github.com/awslabs/amazon-ecr-credential-helper

FROM alpine:3.6

# Commit at Oct 6, 2017, 17:17 PM
ENV APP_VERSION=5aee214c23492c1bf89f655981f8daf9a16267da \
    REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
    METHOD=get

RUN apk add --no-cache ca-certificates
RUN apk --no-cache add --virtual build-dependencies bash gcc g++ musl-dev openssl go git \

    # Install go 1.9
    && GOLANG_VERSION=1.9.2 \
    && GOLANG_SRC_URL=https://golang.org/dl/go$GOLANG_VERSION.src.tar.gz \
    && GOLANG_SRC_SHA256=665f184bf8ac89986cfd5a4460736976f60b57df6b320ad71ad4cef53bb143dc \
    && export GOROOT_BOOTSTRAP="$(go env GOROOT)" \
    && wget -q "$GOLANG_SRC_URL" -O golang.tar.gz \
    && echo "$GOLANG_SRC_SHA256  golang.tar.gz" | sha256sum -c - \
    && tar -C /usr/local -xzf golang.tar.gz \
    && cd /usr/local/go/src \
    && wget -q https://raw.githubusercontent.com/docker-library/golang/master/1.9/alpine3.6/no-pic.patch \
    && patch -p2 -i no-pic.patch \
    && export GOPATH=/go \
    && mkdir -p /go/src \
    && ./make.bash \
    && chmod -R 777 /go \

    # Build Amazon ECR credential-helper
    && APP_REPO=github.com/awslabs/amazon-ecr-credential-helper \
    && git clone https://$APP_REPO $GOPATH/src/$APP_REPO \
    && cd $GOPATH/src/$APP_REPO \
    && git checkout $APP_VERSION \
    && GOOS=linux CGO_ENABLED=0 /usr/local/go/bin/go build -installsuffix cgo \
       -a -ldflags '-s' -o /usr/bin/docker-credential-ecr-login \
       ./ecr-login/cli/docker-credential-ecr-login \

    # Clean up
    && cd / \
    && apk del --purge -r build-dependencies \
    && rm -rf /usr/local/go /usr/lib/go /go /golang.tar.gz /var/cache/* /var/lib/apk*

ENTRYPOINT ["/bin/sh"]
CMD ["-c", "echo $REGISTRY | /usr/bin/docker-credential-ecr-login $METHOD"]

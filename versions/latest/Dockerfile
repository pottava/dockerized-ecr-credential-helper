# Amazon ECR credential-helper
# @see https://github.com/awslabs/amazon-ecr-credential-helper

FROM alpine:3.4
MAINTAINER @pottava

ENV GOPATH=/go \
    PATH=/go/bin:$PATH \
    APP_VERSION=77ede7e8c97aab375302fc8770a7a88a5a1af275

RUN apk add --no-cache ca-certificates \
      && apk --no-cache add --virtual build-dependencies go git \
      && mkdir -p /go/src /go/bin \
      && chmod -R 777 /go \
      && mkdir -p $GOPATH/src/github.com/awslabs \
      && cd $GOPATH/src/github.com/awslabs \
      && git clone https://github.com/awslabs/amazon-ecr-credential-helper \
      && cd amazon-ecr-credential-helper/ \
      && git checkout $APP_VERSION \
      && GOOS=linux GO15VENDOREXPERIMENT=1 CGO_ENABLED=0 \
         go build -installsuffix cgo -a -ldflags '-s' \
         -o $GOPATH/bin/docker-credential-ecr-login \
         ./ecr-login/cmd/ \
      && apk del --purge build-dependencies \
      && rm -rf /go/pkg /go/src

ENTRYPOINT ["/bin/sh"]
CMD ["-c", "echo $REGISTRY | /go/bin/docker-credential-ecr-login $METHOD"]

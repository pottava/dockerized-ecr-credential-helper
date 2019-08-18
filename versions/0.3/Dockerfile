# Amazon ECR credential-helper
# @see https://github.com/awslabs/amazon-ecr-credential-helper

FROM alpine:3.10 AS certs
RUN apk --no-cache add "ca-certificates=20190108-r0"

FROM golang:1.12.7-alpine3.10 AS build
RUN apk --no-cache add "git=2.22.0-r0" gcc g++ musl-dev
RUN go get -u github.com/awslabs/amazon-ecr-credential-helper/...
WORKDIR /go/src/github.com/awslabs/amazon-ecr-credential-helper
RUN git checkout "v0.3.1"
ENV CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=amd64
RUN go build -ldflags "-s -w" -installsuffix cgo -a -o /ecr-login \
    ./ecr-login/cli/docker-credential-ecr-login

FROM alpine:3.10
ENV REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
    METHOD=get
COPY --from=certs /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=build /ecr-login /usr/bin/docker-credential-ecr-login
ENTRYPOINT ["/bin/sh"]
CMD ["-c", "echo $REGISTRY | /usr/bin/docker-credential-ecr-login $METHOD"]

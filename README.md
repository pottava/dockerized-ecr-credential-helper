# Dockerized Amazon ECR credential helper

## Supported tags and respective `Dockerfile` links

・latest ([versions/0.3/Dockerfile](https://github.com/pottava/dockerized-ecr-credential-helper/blob/master/versions/0.3/Dockerfile))  
・0.3 ([versions/0.3/Dockerfile](https://github.com/pottava/dockerized-ecr-credential-helper/blob/master/versions/0.3/Dockerfile))  
・beta ([versions/beta/Dockerfile](https://github.com/pottava/dockerized-ecr-credential-helper/blob/master/versions/beta/Dockerfile))  

([日本語はこちら](https://github.com/pottava/dockerized-ecr-credential-helper/blob/master/README-ja.md))

## Installation

### 1. Test this helper's behavior

* Case 1: with EC2 instance profile

```sh
$ docker run --rm \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  pottava/amazon-ecr-credential-helper:0.3
```

* Case 2: with environment variables

```sh
$ docker run --rm \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  -e AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY \
  pottava/amazon-ecr-credential-helper:0.3
```

* Case 3: with AWS credentials

```sh
$ docker run --rm \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  -v $HOME/.aws/credentials:/root/.aws/credentials \
  pottava/amazon-ecr-credential-helper:0.3
```

### 2. Place a shell script on your $PATH

* Case 1: with EC2 instance profile

```sh
$ sudo sh -c 'cat << EOF > /usr/bin/docker-credential-ecr-login
#!/bin/sh
SECRET=\$(docker run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  pottava/amazon-ecr-credential-helper:0.3)
echo \$SECRET | grep Secret
EOF'
$ sudo chmod +x /usr/bin/docker-credential-ecr-login
```

* Case 2: with environment variables

```sh
$ sudo sh -c 'cat << EOF > /usr/bin/docker-credential-ecr-login
#!/bin/sh
SECRET=\$(docker run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  -e AWS_ACCESS_KEY_ID \\
  -e AWS_SECRET_ACCESS_KEY \\
  pottava/amazon-ecr-credential-helper:0.3)
echo \$SECRET | grep Secret
EOF'
$ sudo chmod +x /usr/bin/docker-credential-ecr-login
```

* Case 3: with AWS credentials

```sh
$ sudo sh -c 'cat << EOF > /usr/bin/docker-credential-ecr-login
#!/bin/sh
SECRET=\$(docker run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  -v $HOME/.aws/credentials:/root/.aws/credentials \\
  pottava/amazon-ecr-credential-helper:0.3)
echo \$SECRET | grep Secret
EOF'
$ sudo chmod +x /usr/bin/docker-credential-ecr-login
```

### 3. Set contents of your ~/.docker/config.json to be

```json
{
    "credsStore": "ecr-login"
}
```

## Usage

Set environment variables if you needed.  

```console
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

There is no need to use `eval "$(aws ecr get-login)"`.  

* `docker push 123457689012.dkr.ecr.us-east-1.amazonaws.com/my-repo:tag`
* `docker pull 123457689012.dkr.ecr.us-east-1.amazonaws.com/my-repo:tag`

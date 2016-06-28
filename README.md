## Supported tags and respective `Dockerfile` links:

・latest ([versions/latest/Dockerfile](https://github.com/pottava/dockerized-ecr-credential-helper/blob/master/versions/latest/Dockerfile))

# Installation

## 1. Place a shell script on your $PATH

* with EC2 instance profile  

```
sudo bash -c 'cat << EOF > /usr/local/bin/docker-credential-ecr-login
#!/bin/sh
docker run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  pottava/amazon-ecr-credential-helper
EOF'
sudo chmod +x /usr/local/bin/docker-credential-ecr-login
```

* with environment variables  

```
sudo bash -c 'cat << EOF > /usr/local/bin/docker-credential-ecr-login
#!/bin/sh
docker run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  -e AWS_REGION \\
  -e AWS_ACCESS_KEY_ID \\
  -e AWS_SECRET_ACCESS_KEY \\
  pottava/amazon-ecr-credential-helper
EOF'
sudo chmod +x /usr/local/bin/docker-credential-ecr-login
```

* with credentials  

```
sudo bash -c 'cat << EOF > /usr/local/bin/docker-credential-ecr-login
#!/bin/sh
docker run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  -v $HOME/.aws/credentials:/root/.aws/credentials \\
  pottava/amazon-ecr-credential-helper
EOF'
sudo chmod +x /usr/local/bin/docker-credential-ecr-login
```

## 2. Set contents of your ~/.docker/config.json to be:

```
{
    "credsStore": "ecr-login"
}
```

# Usage

`docker push 123457689012.dkr.ecr.us-east-1.amazonaws.com/my-repo:tag`

`docker pull 123457689012.dkr.ecr.us-east-1.amazonaws.com/my-repo:tag`

There is no need to use `eval "$(aws ecr get-login)"`.

## Supported tags and respective `Dockerfile` links:

・latest ([versions/latest/Dockerfile](https://github.com/pottava/dockerized-ecr-credential-helper/blob/master/versions/latest/Dockerfile))

([日本語はこちら](https://github.com/pottava/dockerized-ecr-credential-helper/blob/master/README-ja.md))

# Installation

## 1. Test this helper's behavior

* Case 1: with EC2 instance profile  

```
docker run --rm \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  pottava/amazon-ecr-credential-helper
```

* Case 2: with environment variables  

```
docker run --rm \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  -e AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY \
  pottava/amazon-ecr-credential-helper
```

* Case 3: with AWS credentials  

```
docker run --rm \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  -v $HOME/.aws/credentials:/root/.aws/credentials \
  pottava/amazon-ecr-credential-helper
```

## 2. Place a shell script on your $PATH

* Case 1: with EC2 instance profile  

```
sudo sh -c 'cat << EOF > /usr/bin/docker-credential-ecr-login
#!/bin/sh
SECRET=\$(docker --config /dev/null run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  pottava/amazon-ecr-credential-helper)
echo \$SECRET | grep Secret
EOF'
sudo chmod +x /usr/bin/docker-credential-ecr-login
```

* Case 2: with environment variables  

```
sudo sh -c 'cat << EOF > /usr/bin/docker-credential-ecr-login
#!/bin/sh
SECRET=\$(docker --config /dev/null run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  -e AWS_ACCESS_KEY_ID \\
  -e AWS_SECRET_ACCESS_KEY \\
  pottava/amazon-ecr-credential-helper)
echo \$SECRET | grep Secret
EOF'
sudo chmod +x /usr/bin/docker-credential-ecr-login
```

* Case 3: with AWS credentials  

```
sudo sh -c 'cat << EOF > /usr/bin/docker-credential-ecr-login
#!/bin/sh
SECRET=\$(docker --config /dev/null run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  -v $HOME/.aws/credentials:/root/.aws/credentials \\
  pottava/amazon-ecr-credential-helper)
echo \$SECRET | grep Secret
EOF'
sudo chmod +x /usr/bin/docker-credential-ecr-login
```

## 3. Set contents of your ~/.docker/config.json to be:

```
{
    "credsStore": "ecr-login"
}
```

# Usage

Set environment variables if you needed.  
```
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

There is no need to use `eval "$(aws ecr get-login)"`.  

- docker push  
`docker push 123457689012.dkr.ecr.us-east-1.amazonaws.com/my-repo:tag`

- docker pull  
`docker pull 123457689012.dkr.ecr.us-east-1.amazonaws.com/my-repo:tag`

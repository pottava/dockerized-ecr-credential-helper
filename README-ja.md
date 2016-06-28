## 有効なタグと、そのビルドに使われている `Dockerfile`

・latest ([versions/latest/Dockerfile](https://github.com/pottava/dockerized-ecr-credential-helper/blob/master/versions/latest/Dockerfile))

# インストール

## 1. ヘルパーの挙動を確認します

* Case 1: EC2 インスタンスロールを使う場合  

```
docker run --rm -e METHOD=get \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  pottava/amazon-ecr-credential-helper
```

* Case 2: 環境変数を使う場合  

```
docker run --rm -e METHOD=get \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  -e AWS_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY \
  pottava/amazon-ecr-credential-helper
```

* Case 3: クレデンシャルファイルを使う場合  

```
docker run --rm -e METHOD=get \
  -e REGISTRY=123457689012.dkr.ecr.us-east-1.amazonaws.com \
  -v $HOME/.aws/credentials:/root/.aws/credentials \
  pottava/amazon-ecr-credential-helper
```

## 2. $PATH の通っているフォルダにスクリプトを設置 

* Case 1: EC2 インスタンスロールを使う場合  

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

* Case 2: 環境変数を使う場合  

```
sudo bash -c 'cat << EOF > /usr/local/bin/docker-credential-ecr-login
#!/bin/sh
docker run --rm \\
  -e METHOD=\$1 \\
  -e REGISTRY=\$(cat -) \\
  -e AWS_ACCESS_KEY_ID \\
  -e AWS_SECRET_ACCESS_KEY \\
  pottava/amazon-ecr-credential-helper
EOF'
sudo chmod +x /usr/local/bin/docker-credential-ecr-login
```

* Case 3: クレデンシャルファイルを使う場合  

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

## 3. ~/.docker/config.json に以下の値をセット

```
{
    "credsStore": "ecr-login"
}
```

# 使い方

環境変数を使う場合は、そのセット。  
```
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

次のコマンドは使う必要はありません: `eval "$(aws ecr get-login)"`.  
docker push や pull が通常通り利用できます。

- docker push  
`docker push 123457689012.dkr.ecr.us-east-1.amazonaws.com/my-repo:tag`

- docker pull  
`docker pull 123457689012.dkr.ecr.us-east-1.amazonaws.com/my-repo:tag`

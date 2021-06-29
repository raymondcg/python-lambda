# local-aws

* This is an example python lambda docker project. The real ones will be standalone git projects, and build / deployed seperately. 

## Based on Guide
* [Lambda](https://dashbird.io/blog/deploying-aws-lambda-with-docker/)

## Local Build / Test

* Build

```bash
docker build -t necrobraska/lambda-docker-etl:latest .
docker run --rm -d -p 9000:8080 --name lambda-docker-etl necrobraska/lambda-docker-etl
docker logs -f lambda-docker-etl
```

* Test - Windows

```bash
Invoke-RestMethod -Uri "http://localhost:9000/2015-03-31/functions/function/invocations" -Method POST -Body ({} | ConvertTo-Json)
```

* Test - Linux

```bash
curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" -d '{}'
```

## To Make available to AWS

* Obviously change out the URL, Username, and Password for your own. As well as make this a part of the build pipeline.

```bash
aws ecr create-repository \
    --repository-name lambda-docker-etl \
    --image-scanning-configuration scanOnPush=true
    
docker tag lambda-docker-etl:latest \
    123456789.dkr.ecr.eu-central-1.amazonaws.com/lambda-docker-etl:latest
    
aws ecr get-login-password | docker login --username AWS \
    --password-stdin 123456789.dkr.ecr.eu-central-1.amazonaws.com

docker push 123456789.dkr.ecr.eu-central-1.amazonaws.com/lambda-docker-etl:latest
```

## Once it's been pushed to the ECR it can be used in your lambda function.

* TBD
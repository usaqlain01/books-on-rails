# Docker on Amazon ECS using AWS CloudFormation & CLI


Create and run docker container on Amazon ECS using CloudFormation and CLI.

- Containerize a simple REST API application
- Use AWS CLI to create Amazon ECR repository
- Build docker image and push to ECR
- CloudFormation stack to create VPC, Subnets, InternetGateway etc
- CloudFormation stack to create IAM role
- CloudFormation stack to create ECS Cluster, Loadbalancer & Listener, Security groups etc
- CloudFormation stack to deploy docker container


## Terminal Window Logs

### Code

```
mkdir ~/projs
git clone https://github.com/usaqlein/books-on-rails.git books-on-rails
cd book-on-rails
```

### Dockerize a simple app

```
# Run on local
docker build -t books-api ./app/
docker run -it -p 4567:4567 --rm books-api:latest
open http://localhost:4567/
open http://localhost:4567/stat
open http://localhost:4567/api/books
```

### Push Docker Image to ECR

```
aws ecr create-repository --repository-name books-api
aws ecr get-login --no-include-email | sh
IMAGE_REPO=$(aws ecr describe-repositories --repository-names books-api --query 'repositories[0].repositoryUri' --output text)
docker tag books-api:latest $IMAGE_REPO:v1
docker push $IMAGE_REPO:v1
```

### Create CloudFormation Stacks

```
aws cloudformation create-stack --template-body file://$PWD/infra/vpc.yml --stack-name vpc

aws cloudformation create-stack --template-body file://$PWD/infra/iam.yml --stack-name iam --capabilities CAPABILITY_IAM

aws cloudformation create-stack --template-body file://$PWD/infra/app-cluster.yml --stack-name app-cluster

# Edit the api.yml to update Image tag/URL under Task > ContainerDefinitions and,
aws cloudformation create-stack --template-body file://$PWD/infra/api.yml --stack-name api
```

Copy the `BooksApiEndpoint` value from `api` stack output on AWS Management Console. Make a request to that URL on browser or any REST client.

## Author: Usman Saqlain



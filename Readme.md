# About

1. This repository will provide you Docker proxy image which will dynamicly genrate nginx configration for proxy_pass based on Ports you expose
2. It will provide Nginx status page on diffrent port if you expose port 8888

# Requirements

1. Install [Docker](http://docker.io).
2. Install [docker-compose](https://docs.docker.com/compose/install/).
3. Clone this repository

# Setup

## How to deploy in local ?

### build image
```bash
$ cd <root of repository>
$ docker build -t proxy:latest .
```

### run docker container
```bash
$ docker-compose up -d
```
Once your yask is in running state try open URL of [Status](http://localhost:8888)


## run Proxy using Amazon ECS


### create a ECR repository to store image
to create a docker image go to AWS console > EC2 Container Service > Repositories

#### build image : 
```bash
$ cd <root of repository>
$ docker build -t proxy:latest .
```
### push image to ECR :

#### login
```bash
$ aws ecr get-login --region <aws-region>
```
This will provide you a long login command for AWS (Note : this will expiry after 8 hrs so you need to relogin)

#### tag Image
```bash
$ docker tag proxy:latest <ecr-reposiotory-url>:latest
```

#### push image
```bash
$ docker push <ecr-reposiotory-url>:latest
```
#### create a task defination 
sample file for the ECS task defination is provided in the folder ecs inside this repostitory, you need to change some value in that defination

```bash
	"containerDefinitions": [{
		"volumesFrom": [],
		"memory": 1024, <<< change the RAM based on your requirment
		"extraHosts": null,
		"dnsServers": null,
		"disableNetworking": null,
		"dnsSearchDomains": null,
```

```bash
		"workingDirectory": null,
		"readonlyRootFilesystem": null,
		"image": "123456789123.dkr.ecr.us-east-1.amazonaws.com/proxy:latest", <<< change image with URL of your own ECR image
		"command": null,
```

once you made all require change go to AWS console > EC2 Container Service > Task Definitions and Create a Task Definition

#### create a Service

to create a service go to Newely created task defination and from Actions menu create service 

Fill below value based on you requirment and Create Service 
```bash
Cluster
Service name
Number of tasks
Minimum healthy percent
Maximum percent
```
Once Done new service will be added with number of task in your selected cluster

#### Check ELK

Once your yask is in running state try open URL of [Status](http://localhost:8888)

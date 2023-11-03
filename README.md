# Cloud Training
This is COHORT 5 cloud training

## AWS Regions
- Factor for selecting a region for provsioning the resources
- Workload consumers location

### AWS Global and Region scoped services

- AWS has some services that are global, meaning they are not limited by region
  - IAM
  - Route 53 (DNS)
  - CloudFront
- AWS servises are region0-scoped like lambda, EC2 and more!
- ![AWS Services by Region](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)

## IAM

- Billing IAM
- Polciy for each IAM account
- Fedration for accpount management
- Manage policy  
- Permission boundry 
  

## IAM policy
- Inline or manage policy
- Deny always Wins instead of ALLOW 
- Policy lives within AWS account.


## Managing multiple Account - AWS Organizations
- SCP Service control policy can be applied by any node
- Master/Management Account remains unaffected by SCPs 

## New AWS account creation
New AWS account creation process
  - Independent account creation
  - Under AWS Oraganization
**Why are limits/Quota important?**

## Users and Groups
  - Groups cannot be part of other groups
  - only Users can be added to the group.

## Sample code enable autocomplete for AWS
``` 
echo -e "\nexport PATH=/usr/local/bin/:\$PATH\n" >> .profile 
complete -C '/usr/local/bin/aws_completer' aws
cat ~/.profile
```
## create the role 
-  Create role **role-npls** without any access or policy attached.
-  create profile to allow list IAM user

```

{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "VisualEditor0",
			"Effect": "Allow",
			"Action": "iam:ListUsers",
			"Resource": "*"
		}
	]
}

```

## Create EC2 instance and connect to the Instance

```

[ec2-user@ip-172-31-26-224 ~]$ aws configure
AWS Access Key ID [None]: 
AWS Secret Access Key [None]: 
Default region name [None]: us-west-2
Default output format [None]: json
[ec2-user@ip-172-31-26-224 ~]$ ***aws sts get-caller-identity***
{
    "UserId": "ZXCWWEDSDSD242424AA:i-073b3b953f37c8dfd",
    "Account": "99999999999",
    "Arn": "arn:aws:sts::99999999999:assumed-role/role-npls/i-073b3b953f37c8dfd"
}

```

## IAM Assume role
- create policy with STS assume role to allow swtich roles 
- Create EC2 assume with example for terraform
```
data "aws_iam_policy_document" "ec2_assume_role_policy" {
  statement {
    actions = [
      "sts:AssumeRole",
    ]

    principals {
      type        = "Service"
      identifiers = ["ec2.amazonaws.com"]
    }
  }
}
```


## Permission boundary
- User action will be restricted from access AWS service
- Priviledge escalation
- Effective permissions with boundry 
- actual permission of the user is effectively limited by the permssion boundry.
- Permission boundry is configured to allow or deny AWS service or certain function


## IAM vs AWS cognito
- IAM is for cloud access management
- AWS cognito allow to manage credentails and application can use the service to authenticate


## Setting up VM and VSCODE
- Setup .profile with Access_key,secret key and aws region 
- source .profile to setup the access to AWS 
- Run command ```aws sts get-caller-identity``` below is sample output
```
training@ip-172-31-21-113:~/cloud-training/cloud-training$ aws sts get-caller-identity
{
    "UserId": "AIDA6AK5C3H75ALAMT77",
    "Account": "96981224699607",
    "Arn": "arn:aws:iam::96981224699607:user/participant-17"
}
```
- Add two extension to VS CODE
  - terraform
  - docker


# Excercise 2b:
- create new policy with permission shown 
- create new users policy-user
- Attach policy to the newly created user
- Create new policy with below policy:
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "VisualEditor0",
			"Effect": "Allow",
			"Action": [
				"dynamodb:PutItem",
				"dynamodb:GetItem",
				"dynamodb:UpdateItem"
			],
			"Resource": "*"
		}
	]
}
```

## Ec2 instances 
- EC2 Pricing
  - On-demand
    - Per hour pricing
  - Reserved
  - Spot Instances
- EC2 and Auto Scaling Labs
  - create EC2 Instance 
  - create IAM Image

### EC2 Rules
- Security group with inbound from remote ssh
- Key pair selected
- 


## RDS
- Resilient Durable Application Archtitechture 
- Disater recovery vai multi-AZ Setup 
- Manual Scaling - both vertical and horizontal 
- Vertical auto-scaling not support 
  - Storage Auto scaling can be enable
- RDS read Replica 
- Allow port 3306 from private IP on instance
- Create SG to allow EC2 instance to connect.

```mermaid
graph TD;
    Master-->Read-only-1
    Master-->Read-only-2;
    Master-->Read-only-3;
```
 
## Aurora RDS 
- Scale-out, distributed archtiecture
- Multi-AZ will have shared storage within 2-3 AZ's 
- Underline shared storage.
  
## S3 Storage
- Durabilty is not same as availablity
- Durability is probability that the object will remain intact and accessible after a period of year
- 100% durability - no possibility of the object being lost
- 90% - there's a 1 in 10 chance being lost
- 11 9's 99.99999999999%
- S3 gurantees 11 9's of durability 
- Out of 1 million object loss one file in 10 years
- S3 buckets have policy as well 
- globally unique filename
- WORM (write only read mulitple ) usage 
- Uses global namespaces
  
### S3 Storage class
- S3 Standard 
  - Default class
  - Active, Frequently accessed data
  - $0.0210/GBkv
  - >= 3 AZ's
- S3 intelligent-Tiering
  - Data with changing access pattern


|S3 Standard|S3 Intelligent Tiering|S3 Standard IA| S3 One Zone IA| S3 Glacier| S3 Glacier Deep Archive|
|:----|:----|:----|:----|:----|:----| 
|<ol><li>Infrequent</li><li>Active, frequently accessed data</li><li>Milliseconds access</li><li>> 3 AZ</li><li>Cost $0.0210/GB/month</li></ol>|<ol><li>Data with changing access patterns</li><li>Milliseconds access</li><li>>3 AZ</li><li>Cost: $0.0210 to $0.0125/GB</li><li>Monitoring fee per obj.</li><li>Min. storage duration</ol>|<ol><li>Infrequently accessed data</li><li>Milliseconds access</li><li>>3 AZ</li><li>Cost: $0.0125/GB</li><li>Retrieval fee per GB</li><li>Min. storage duration</li><li>Min. object size</ol>|<ol><li>Re-creatable, less accessed data</li><li>Milliseconds access</li><li>1AZ</li><li>Cost: $0.0100/GB</li><li>Retrieval fee per GB</li><li>Min. storage duration</li><li>Min. object size</ol>|<ol><li>Archive data</li><li>Select minutes or hours</li><li>>3 AZ</li><li>Cost: $0.0040/GB</li><li>Retrieval fee per GB</li><li>Min. storage duration</li><li>Min. object size</ol>|<ol><li>Archive data</li><li>Select 12 or 48 hours</li><li>>3 AZ</li><li>Cost: $0.00099/GB</li><li>Retrieval fee per GB</li><li>Min. storage duration</li><li>Min. object size</ol>|


## VPC 
- CIDR calculation for example (2^(32-24) = 2^8 = 256)
- CIDR Block : 10.0.0.0/24 
  - Start: 10.0.0.0
  - Last: 10.0.0.255
- CIDR Block : 10.0.0.0/26
  - Start - 10.0.0.0
  - Last - 10.0.0.63
- Example VPC creation:
  - CIDR block: 10.0.0.0/28
    - pub-a: 10.0.0.15/28
    - pub-b: 10.0.0.16/28
    - prv-a: 10.0.0.32/28
    - prv-b: 10.0.0.48/28
- VPC endpoint help the service to allow connectivity 
  - Enable Users to privately connect VPC tp support AWS services (like S3)
- VPC and regions
  - VPCs are specific to a given region
  - i.e. - a single VPC can not span multiple regions
  - However, it’s common to have separate VPCs in different regions for performance, compliance and DR purposes
  - VPC peering - can connect VPCs in different regions using this
  - Default limit of VPCs: 5 per region per account
- Subnets facts
  - Subnets segment VPC address ranges even further.
  - Subnets can exist within one and only one Availability Zone.
  - Subnet CIDR blocks within a VPC must not overlap.
  - Subnet inbound and outbound traffic can be restricted using NACLs.
  - Recommendation: Allocate substantially more IPs for private subnets than for public subnets.
- Internet Gateway
  - Allows resources (e.g., EC2 instances) in a VPC connect to the Internet
  - It scales horizontally and is highly available and redundant
  - Must be created separately from a VPC
  - One VPC can only be attached to one IGW and vice versa
  - Internet Gateways on their own do not allow Internet access...
  - Route tables must also be edited!
- NAT Gateway 
  - NAT gateway is created in each AZ
  - Elastic IP is allocated to NAT gateway
  - 

## ElastiCache
- The same way RDS is to get managed Relational Databases, ElastiCache is to get managed Redis or Memcached
- Caches are in-memory databases with high performance, low latency
- Helps reduce load off databases for read intensive workloads
- AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups
- Redis vs Memcache
  
|Redis|Memcached|
|:-----|:---------|
|<ul><li>Sub-millisecond latency</li><li>Supports complex data types (sorted sets,hashes, bitmaps, hyperlog, geospatial index)</li><li>Multi AZ with Auto-Failover, supportssharding</li><li>Read Replicas for scalability and HA</li><li>Data Durability using AOF persistence</li><li>Backup and restore features</li></ul>|<ul><li>Sub-millisecond latency</li><li>Support only simple data types (string, objects)</li><li>Multi-node for sharding</li><li>Non persistent</li><li>No backup and restore</li><li>Multi-threaded architecture</li></ul>|


## DynamoDB
key consideration for dynamoally db service:
- Enventual consistency 
- Strong consistency 
- Potential usage: quick lookup with high concurrency with sub milliseconds response 


## Infrastructure as Code
- Cloud Formation - IaC for AWS was released in 2011, which of course only worked with AWS
- However, the need for a cloud-agnostic IaC provider was still present
- So that we could provision resources across different providers such as Microsoft Azure, GCP, in addition to AWS
  
### Teraform config
- Use profile in provider template file for autehnticate
- Use tfvar files to register 


## Serverless
- Serverless does nopically t mean that there are no servers
- Rather, it's a paradigm in which developers no longer have to directly manage servers
- But servers are still being used in the background - you as a developer just do not see them
- API gateway, Fargate, step functions 
- Essentially anything  that is managed service is considered serverless b/c you typically 
- AWS Serverless Service
  - AWS lamdba
  - Fargate - Serverss for ECS-EKS provides infrastruture to run docker containers
  - S3 - storage solution
  - More info is available at [https://aws.amazon.com/serverless/](https://aws.amazon.com/serverless/)

### Lambda
Lambda - why do we use them?
- Now, let's talk a bit more about ***lambdas...***
- Lambdas are ***virtual functions***, which means that there are no servers to manage
- Because lambdas ***run on-demand*** - only when invoked, this is very different than the EC2 cost model
- Lambdas have ***short execution*** periods - default of 3 seconds, with a max of 15 minutes (you can adjust this value within that range
- With Lambdas, ***scaling is automated***, if you need more lambda functions, AWS will automatically provision them for you

### Lambda case study - Netflix
|Statistic|Existing Ec2|Enhanced Lambda|
|:----|:----|:----| 
|Average Time to First <br/> Response of New Instance|3 minutes|2006 ms|
|Average Response Time of <br/>Warmed Instance|800 ms|1100 ms|
|Average Active Instances / Concurrent Executions|~1000 instances|<100 Preprocessor instances<br/><25 Concurrent Executions in Lambda|
|Average Daily Costs|<$100|<$100|

### Lambda use containers under the hood?

__**Yes and no! Per the doc here:**__
[https://dl.awsstatic.com/whitepapers/Overview-AWS-Lambda-Securitv.pdf](https://dl.awsstatic.com/whitepapers/Overview-AWS-Lambda-Securitv.pdf)
```
Execution environments are isolated from one another using several container-like technologies built into the Linux kernel, along with A WS proprietary isolation technologies. These technologies include: eg roups, namespaces, ch root, etc.. 
```

## AWS API Gateway
- AWS Lambda + API Gateway: No infrastructure to manage
- Support for the WebSocket Protocol
- Handle API versioning (v I, v2...)
- Handle different environments (dev,test, prod...)
- Handle security (Authentication and Authorization)
- Create API keys, handle request throttling
- Swagger / Open API import to quickly define APIs
- Transform and validate requests and responses
- Generate SDK and API specifications
- Cache API responses

## API Gateway Integrations - High Level
- Lambda Function
  - Invoke Lambda function
  - Easy way to expose REST API backed byAWS Lambda
- HTTP
  - Expose HTTP endpoints in the backend
  - Example: internal HTTP API on premise, Application Load Balancer...
  - Why? Add rate limiting, caching, user authentications, API keys, etc...
- AWS Service
  - Expose any AWS API through the API Gateway
  - Example: start an AWS Step Function workflow, post a message to SQS
  - Why? Add authentication, deploy publicly, rate control...


## Cloud Native

__**Cloud Native - what does that mean?**__

- **Story time**: when the cloud started to become popular in the mid 2000s (2005 or
so) everyone started to throw their applications on the cloud using the lift and shift
strategy - hoping to get the cost/performance benefits the cloud promised
  - *Key takeaway* - lift and shift == migrating to cloud w/ minimal changes to the
application before the migration
- But they found they were often getting ***higher costs and worse performance***, so
they soon realized that in order to get the benefits of running on the cloud they
had to **first re-architect** *their applications to run effectively on the cloud*
- And hence the term **cloud native** was born - basically it means that if your app is
cloud native then it is specifically architected to run on the cloud, and you can
therefore get the benefits of **lower costs/better performance** that the cloud
promises

### Cloud native - characteristics
- **The Twelve-Factor App**: Baseline best practices for designing & building web applications for portability and resilience.
  - [Reference: https://12factor.net/](https://12factor.net/)
- **Microservices**: Independently deployable services that do one thing well (Single Responsibility Principle).
- **Self-Service Infrastructure**: Platforms for rapid, repeatable, and consistent provisioning of ap^ environments and backing services.
- **API-based Collaboration**: Published and versioned APIs that allow interaction between services.
- **Anti-Fragile**: Systems that are resilient to stress and failure.

## Microservices 
__**Microservices + Monolith - definition**__
- To best define microservices, I think it helps to first compare it to the term
monolith
- A monolithic application is basically one gigantic application sharing a single
codebase (think: a single Git repository)
- What challenges would we face if for example, we were running all of
Amazon.com in a single codebase - a single Github repository perhaps?

### Monolith - solution...
- By breaking something down into more manageable pieces - microservices - we can better tackle the problem and deal with complexity.
- Microservices are the “smaller pieces” of the larger application

### Microservices characteristics
- Small, fully independent and autonomous application. Isolated from other MS’s, maybe even with own database.
- Responsible for a logical part of a larger application “ecosystem”. Singular responsibility.
- Can be any language. Developers on the same project can have completely different skill sets if necessary.
- Loosely coupled to other MS’s. Communicate with other MS’s using standardized protocols. Often a REST API or a message bus.
- People generally start with coarse grained services and then break them up into smaller and smaller pieces as the time goes by and they know more and more

### Microservices benefits
- By dividing and conquering w/ microservices, if we want to make a small change to the site, we would just update the microservice that is responsible for that portion of the site
- And the key here is that we would only need to redeploy that particular microservice, without disturbing/interfering with all the other stuff that powers amazon.com.
- This is as opposed to using a monolith where we would have to redeploy all of amazon.com for a small change which doesn’t make sense of course.
- __*One of the clear advantages of using microsen/ices is that it allows us to be <strong>more agile</strong>*__
  - *Agile means we can deploy quickly when changes need to be made, and respond to  the needs of the business faster!*


## Containers vs VMs revisited
- Both VMs and containers are essentially *"wrappers"* around our applications making it easier to run our apps on the cloud
- Containers allow us to share a copy of the OS between applications
-	Apps running inside a VM each have their own copy of the OS. Hence, VM’s are heavier
- Containers are lightweight, low overhead, and we can run more apps on a single server with containers
  
*Sidenote: VM’s offer better security than containers due to OS isolation*


## Docker Terminology 
- *DockerFile* is declarative file that contains information about **application code** --> **Docker Image** convert into __***Docker container***__
- *DockerFile* will live in application’s repository with source code.
- The source code + *DockerFile* will give us a Docker image
- Docker images are how we share our application
- An instance of a Docker image is a running container....this is a running instance of your application!
- Don’t worry if this is not clear yet, we will be playing with images and containers shortly..

### Docker Ecosystem
- Docker CLI
- Docker Engine
- Docker Images
  - Image is a template for docker container 

### Docker CLI - images
- `docker build` ... - used to build image from Dockerfile
- `docker pull` ... - to pull an image from a repo
- `docker run` ... - to run an image
- `-it` - interactive and gives you command prompt inside of container
- `-p` - port mapping, exposes port from inside container to hos
- `-e` - environment variable
- `-v` - volume mapping
- `docker images` - this will show you all of your images
- `docker rmi` ... - to delete an image (pass in the image name or id)
  - *Can't delete an image that has a running container (a running instance)*
- `docker ps` - to list running containers
- Use `-a` arg to see stopped containers as well
- `docker inspect`... - to inspect a running container
- `docker logs` ... - to view console output of a container
- `docker rm `... - to remove a container
- `docker start/stop/restart`... - self explanatory
- Difference between stop and rm?
  - A container that's stopped can be restarted, not true if "rm"d (deleted)
  - If you stop a container, you will still see it in docker ps -a , but not if you used rm

#### Docker CLI - port mapping
- By default, when you run a container with docker run, it doesn't publish any of it's ports to the outside world - i.e., your localhost when running Docker Desktop
- Which makes sense because a container is it's own isolated environment!
- So, how do we expose that internal port?
- Through the '-p' flag (p for publish) when we do a docker run
- So, if we do docker run ~p 8080:80 that says map port 80 in the container to
- port 8080 on the Docker host - your localhost in this case
- ```docker run -p``` [host_port]:[container_port] [docker_image]
  - Example: ```docker run -it -p 27017:27017 mongo:4.0 /bin/bash```




### Docker image content
1. Minimal OS layer
2. Install apache and other component 
3. App related :*artifacts*
4. bootstrapping the :**app**


### Networks in Docker
- You can create Docker networks to allow containers to easily communicate.
- **Types**:
  - Bridge (default) - this one is created by Docker and containers go here by default. Not recommended for production. No container communication by name.
  - Bridge (user-defined) - Recommended for standalone containers running on the sam host. Containers can communicate by name.
  - Overlay - allows networking across Docker hosts. Works with Swarm.
- Networks are managed with the docker network ... commands
  - **Docker network** will show all commands,
- example **`docker network ls`** will display all existing networks including 3 pre-defined networks.
- Containers are put into a network with the `--network` option in the docker run command.
- Existing containers can be put into a network with **docker network connect <network name> <container name>**


## Excercise for docker
- List the containers `docker ps`
- Create the application network `docker network create net_wp` 
- List of the network `docker network ls`
```
NETWORK ID     NAME       DRIVER    SCOPE
f21703de9a2f   bridge     bridge    local
fc97393ba5b9   host       host      local
a254a9058b07   minikube   bridge    local
381bd4967dae   net_wp     bridge    local
6fdcbcccde0c   none       null      local
```
- Create mysql Docker container `docker run --name wp-mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=wp -e MYSQL_USER=user -e MYSQL_PASSWORD=admin --net=net_wp -d mysql:latest`
- Creating wp web container `docker run --name wordpress -e WORDPRESS_DB_HOST=wp-mysql:3306 -e WORDPRESS_DB_USER=user -e WORDPRESS_DB_PASSWORD=admin -e WORDPRESS_DB_NAME=wp --net=net_wp -p 8081:80 -d wordpress:latest`


### Dockerfile commands
A sampling of commands you may see in a Dockerfile:
- `FROM` - which image you want to inherit from 
- `RUN` - Run a Linux command in a new layer. The command is dependent on which shell is in the image.
- `EXPOSE` - Tells what ports this container will listen on. Does not actually publish a port, but is rather like documentation.
- `COPY` - copy files into the container.
- `ADD` - copy files into the container (same as COPY, but supports 2 other sources 
        - additional URL source, tar extraction from local)
- `ENV` - Define environment variable
- `ENTRYPOINT` - The command to be run when container is started. This is inherited from a base image.
- `CMD` - provides defaults to be run after the ENTRYPOINT. Also inherited from the base image


## Docker Compose

In the last lab, we saw how to create a docker network manually. Imagine if we had more than 2 containers - the command could get quite long!
Is there a simpler, cleaner way to create a network? Answer: **yes, with Docker Compose**

- Compose is a lightweight way to run your application without the overhead of a Swarm or Kubernetes application.
- Compose is a tool for running multiple related containers together with one command. It is included **with Docker by default**.
- So, you don’t have to start each container separately
- You specify **all the configurations** in **docker-compose.yml** file.
- This is another example of **infrastructure-as-code!**
- Compose creates a new user defined bridge (just like we did manually in networks lab) network for your containers
- This way they can communicate with each other **by name**.
- You can refer to an existing image in Docker Hub, on your local machine, a private repo, or you can refer to a Dockerfile location, and **Compose will build the image for you before running the container**.
- To start up your app, use `docker-compose up -d`
- To stop it, use `docker-compose down`
- It will automatically look at the **docker-compose.yml** file in the current folder location


### Interactive Shell
Containers are mostly intended to run as headless without interaction.
- headless == no interface, not bash shell, etc.
- Sometimes however you may want to interact and provide input
- To interact, you can run attached (or attach later) to interact with it.
- **`docker run -it <image-name>/bin/sh`**
- **`docker attach <container-name>`**
- You can now get it’s output (like with docker logs), provide input if necessary.
- If you attach at run then to detach without stopping it, use the detach sequence (hold CTRL, press p then q), CTRL+ C will end the container process.
- We will see how to ‘login’ in the container next, and how to install software


### Multi-stage builds - stages
- A Dockerfile is multi-stage if you see **multiple FROM** statements:
- This example below has 2 distinct stages
- The second stage references the first stage by using `COPY -from=0`
  - `0 is the first stage`
  - this says just grab what I need from the Oth (first) stage, then put it inside an Alpine OS	/

```Dockerfile
# syntax=docker/dockerfile:1

FROM golang:1.16
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html
COPY app.go ./
RUN CGO_ENABLED=0 Î00 =linux go build -a -installsuffix ego -o app .

FROM alpine:latest
RUN apk —no-cache add ca-certificates
WORKDIR /root/
COPY —from=0 /go/src/github.com/alexellis/href-counter/app ./
CMD ["./app"]
```

### Bind mount versus volumes
- Volumes are stored in a part of the host filesystem which is managed by Docker (`/var/lib/docker/volumes/` on Linux). Non-Docker processes should not modify this part of the filesystem. Volumes are the best way to persist data in Docker.
- Bind mounts may be stored anywhere on the host system. They may even be important system files or directories. Non-Docker processes on the Docker host or a Docker container can modify them at any time.
- Per the docs: “Bind mounts have limited functionality compared to volumes” - [https://docs.docker.com/storage/bind-mounts/](https://docs.docker.com/storage/bind-mounts/)
- Volumes are managed by Docker
- Volumes - remember they put stuff inside the space that Docker manages f
- But bind mounts put stuff inside the actual host machine.- NOT inside the space managed by Docker
  - In other words, if you are running Docker Desktop on a Mac, a bind mount will create a folder on your Mac OS - so you can actually see the bind mount in Finder (same for Windows)

### Docker (Named) Volume vs Bind Mount Syntax
- difference is very subtle
- with named volumes, we just specify a name
- with bind mounts, we specify a path on the host
- backslash is to allow for new lines
- NAMED DOCKER VOLUMES:
- 
```
docker run \
--name=nginxtest \
-v nginx-vol:/usr/share/nginx/html \
nginx: latest
```
- BIND MOUNTS:
```
docker run —name devtest \
-v "$(pwd)"/target :/app \
nginx: latest
```

## AWS Step Functions
- Build serverless visual workflow to orchestrate your **Lambda functions**
- However, lambdas are not the only AWS service you can use step functions with
- Features: sequence, parallel, conditions, timeouts, error handling,
- Possibility of implementing human approval feature - i.e. if human says “yes” then go forward
- However, lambdas are not the only AWS service you can use step functions with
  - Can integrate with ECS, On-premise servers, API Gateway, SQS queues, etc.
- Use cases: order fulfillment, data processing, web applications, any workflow




## KubeCTL inst

```log
training@ip-172-31-21-113:~/cloud-training/dockerlabs/k8s$ kubectl get all
NAME               READY   STATUS    RESTARTS   AGE
pod/my-nginx-pod   1/1     Running   0          39s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   286d

kubectl describe pods my-nginx-pod
Name:             my-nginx-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Wed, 01 Nov 2023 18:51:53 +0000
Labels:           app=nginx
Annotations:      <none>
Status:           Running
IP:               172.17.0.3
IPs:
  IP:  172.17.0.3
Containers:
  nginx-container:
    Container ID:   docker://91be7094b36936f13a1d5e3991902a277971120741b5d2ed0d7bb03d136f79e6
    Image:          nginx:1.17.0
    Image ID:       docker-pullable://nginx@sha256:bdbf36b7f1f77ffe7bd2a32e59235dff6ecf131e3b6b5b96061c652f30685f3a
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 01 Nov 2023 18:51:57 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-78zn6 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-78zn6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  2m55s  default-scheduler  Successfully assigned default/my-nginx-pod to minikube
  Normal  Pulling    2m54s  kubelet            Pulling image "nginx:1.17.0"
  Normal  Pulled     2m51s  kubelet            Successfully pulled image "nginx:1.17.0" in 3.414333128s
  Normal  Created    2m51s  kubelet            Created container nginx-container
  Normal  Started    2m51s  kubelet            Started container nginx-container


```

## K8s - declarative model
- K8s uses infrastructure as code concepts
- Infrastructure as code (laC) means managing your IT infrastructure using configuration files.
- Declarative vs imperative:
  - Declarative is like asking someone to bake a cake and giving them the ingredients without a recipe - the baker will figure it out
  - Imperative is like giving ingredients + recipe - telling baker exactly what to do. i.e. here are the exact steps you need to take to bake a cake

### K8s Replicasets
- What is self healing? When k8s automatically spins up a new pod when on goes down
- Replicasets and deployments are the two ways to achieve self-healing within
- When creating a Replicaset, we don’t need to define the pods as a separate resource - i.e. as a separate pod.yaml file.
- Pods are created within the replicaset and are “owned” by that replicaset
- selector - specifies how to identify Pods it can acquire


### K8s -probe types 
- We will focus on 2 primary probe types: **liveness** and **readiness**
- **Liveness** probes basically are a check that runs periodically (you define how often) and asks your application are you alive, are you alive, etc.?
- If the answer is ”no” k8s will **restart** your pod since that should bring your container/application pod back to life
- If the answer is “yes” nothing will happen since your pod is healthy
- **Readiness** probes are similar except they are basically checks to see if your pod is ready to accept traffic
- Sometimes our applications need to load large data or configuration files during startup, or depend on external services after startup.
- In such cases, you don't want to kill the application, but you don't want to send it requests either
- So, readiness probes will **prevent traffic** from being sent to pods that are not ready to accept it.

#### K8s - Parameters for liveness/readiness probes
- Both readiness and liveness probes have additional parameters
- They are the same exact parameters for both probes
- And if you don’t specify these parameters they do have default values as well:
- ÇinitialDelaySeconds: Probes start running after jnitialDelaySeconds after container is started (default: 0)
- periodSeconds: How often probe should run (default: 10)
- timeoutSeconds: Probe timeout (default: 1)
- successThreshold: Required number of successful probes to mark container healthy/ready (default: 1)
- failureThreshold: When a probe fails, it will try failureThreshold times before deeming unhealthy/not ready (default: 3)


## EKS vs ECS

|ECS|EKS|
|:---|:---|
|Task - contains 1 container typically |Pod - contains 1 contaner typically|
|An individual task will not auto-heal | An indidual pod will not auto-heal|
|Service - maintains n number of tasks | Replicaset - maintains n number of pods|
|An Attrribute in Service which links the ALB| Service -- to Expose out (like a load balance) |


```mermaid
---
title: EKS cluster flow 
---
%%{init: {"flowchart": {"htmlLabels": false}} }%%

flowchart TD
  cluster["`this is **Cluster** `"]
  services1["`**Web Services**`"] 
  tasks11["`**Tasks** (with one task definition)'"] 
  tasks12["`**Tasks** (with one task definition)'"] 
  tasks21["`**Tasks** (with one task definition)'"] 
  tasks22["`**Tasks** (with one task definition)'"] 
  target11["`These tasks gets registered to the **Target Group** `"]
  target12["`These tasks gets registered to the **Target Group** `"]
  target13["`These tasks gets registered to the **Target Group** `"]
  target21["`These tasks gets registered to the **Target Group** `"]
  target22["`These tasks gets registered to the **Target Group** `"]
  target23["`These tasks gets registered to the **Target Group** `"]

  cluster --> services1  
  services1 --> tasks11 
  services1 --> tasks12 
  services1 --> tasks21 
  services1 --> tasks22 
  tasks11 -->  target11
  tasks12 -->  target12
  tasks11 -->  target13
  tasks21 -->  target21
  tasks22 -->  target22
  tasks22 -->  target23

```

```mermaid
---
title: Application Service Task 
---
flowchart LR
  app1["App - A"]
  app2["App - B"]
  service1[" Service One "]
  service2[" Service Two "]
  taskdef1[" Task Definition 1 for <br /> Container Configuration"]
  taskdef2[" Task Definition 2 for <br /> Container Configuration"]
  app1 --> service1 --> taskdef1
  app2 --> service2 --> taskdef2

```

## Tips for load balancer configuration
- network perperspective
  - securty group
- Permission perspective
  - IAM roles setup
  - Policy configuration


## Lab for 24 for EKS 
### the YAML 

```yaml
# An example of ClusterConfig with a normal nodegroup and a Fargate profile.
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: npls-fargate-cluster
  region: us-west-2

  
vpc:
  id: "vpc-04884555a196fcc03"
  cidr: "10.0.0.0/16"
  subnets: # FARGATE Must be Private
    private:
      us-west-2a:
        id: "subnet-0a1f7c28c6be206b6"  
        cidr: "10.0.11.0/24" 
      us-west-2b:
        id: "subnet-00eb1479dca749d62" 
        cidr: "10.0.12.0/24"
      us-west-2c:
        id: "subnet-098f5f0f491115bb4"
        cidr: "10.0.13.0/24"        

      
fargateProfiles:
  - name: fp-default
    selectors:
      # All workloads in the "default" Kubernetes namespace will be
      # scheduled onto Fargate:
      - namespace: default
      # All workloads in the "kube-system" Kubernetes namespace will be
      # scheduled onto Fargate:
      - namespace: kube-system
    subnets: [ "subnet-0a1f7c28c6be206b6", "subnet-00eb1479dca749d62", "subnet-098f5f0f491115bb4" ]
    
  - name: fp-dev
    selectors:
      # All workloads in the "dev" Kubernetes namespace matching the following
      # label selectors will be scheduled onto Fargate:
      - namespace: dev        
    subnets: [ "subnet-0a1f7c28c6be206b6", "subnet-00eb1479dca749d62", "subnet-098f5f0f491115bb4" ]


```

```log
[ec2-user@ip-10-0-2-108 eks-lab]$ eksctl create cluster -f cluster-fargate-npls.yaml 
2023-11-03 18:32:29 [ℹ]  eksctl version 0.164.0
2023-11-03 18:32:29 [ℹ]  using region us-west-2
2023-11-03 18:32:29 [✔]  using existing VPC (vpc-04884555a196fcc03) and subnets (private:map[us-west-2a:{subnet-0a1f7c28c6be206b6 us-west-2a 10.0.11.0/24 0 } us-west-2b:{subnet-00eb1479dca749d62 us-west-2b 10.0.12.0/24 0 } us-west-2c:{subnet-098f5f0f491115bb4 us-west-2c 10.0.13.0/24 0 }] public:map[])
2023-11-03 18:32:29 [!]  custom VPC/subnets will be used; if resulting cluster doesn't function as expected, make sure to review the configuration of VPC/subnets
2023-11-03 18:32:29 [ℹ]  using Kubernetes version 1.27
2023-11-03 18:32:29 [ℹ]  creating EKS cluster "npls-fargate-a2" in "us-west-2" region with Fargate profile
2023-11-03 18:32:29 [ℹ]  will create a CloudFormation stack for cluster itself and 0 nodegroup stack(s)
2023-11-03 18:32:29 [ℹ]  will create a CloudFormation stack for cluster itself and 0 managed nodegroup stack(s)
2023-11-03 18:32:29 [ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=us-west-2 --cluster=npls-fargate-a2'
2023-11-03 18:32:29 [ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "npls-fargate-a2" in "us-west-2"
2023-11-03 18:32:29 [ℹ]  CloudWatch logging will not be enabled for cluster "npls-fargate-a2" in "us-west-2"
2023-11-03 18:32:29 [ℹ]  you can enable it with 'eksctl utils update-cluster-logging --enable-types={SPECIFY-YOUR-LOG-TYPES-HERE (e.g. all)} --region=us-west-2 --cluster=npls-fargate-a2'
2023-11-03 18:32:29 [ℹ]  
2 sequential tasks: { create cluster control plane "npls-fargate-a2", 
    2 sequential sub-tasks: { 
        wait for control plane to become ready,
        create fargate profiles,
    } 
}
2023-11-03 18:32:29 [ℹ]  building cluster stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:32:29 [ℹ]  deploying stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:32:59 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:33:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:34:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:35:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:36:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:37:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:38:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:39:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:40:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:41:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:42:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:43:30 [ℹ]  waiting for CloudFormation stack "eksctl-npls-fargate-a2-cluster"
2023-11-03 18:45:31 [ℹ]  creating Fargate profile "npls-fp-default" on EKS cluster "npls-fargate-a2"
2023-11-03 18:49:48 [ℹ]  created Fargate profile "npls-fp-default" on EKS cluster "npls-fargate-a2"
2023-11-03 18:49:48 [ℹ]  creating Fargate profile "npls-fp-dev" on EKS cluster "npls-fargate-a2"
2023-11-03 18:50:06 [ℹ]  created Fargate profile "npls-fp-dev" on EKS cluster "npls-fargate-a2"
2023-11-03 18:50:36 [ℹ]  "coredns" is now schedulable onto Fargate
2023-11-03 18:51:39 [ℹ]  "coredns" is now scheduled onto Fargate
2023-11-03 18:51:39 [ℹ]  "coredns" pods are now scheduled onto Fargate
2023-11-03 18:51:39 [ℹ]  waiting for the control plane to become ready
2023-11-03 18:51:39 [✔]  saved kubeconfig as "/home/ec2-user/.kube/config"
2023-11-03 18:51:39 [ℹ]  no tasks
2023-11-03 18:51:39 [✔]  all EKS cluster resources for "npls-fargate-a2" have been created
2023-11-03 18:51:40 [ℹ]  kubectl command should work with "/home/ec2-user/.kube/config", try 'kubectl get nodes'
2023-11-03 18:51:40 [✔]  EKS cluster "npls-fargate-a2" in "us-west-2" region is ready
[ec2-user@ip-10-0-2-108 eks-lab]$ **kubectl get nodes**
NAME                                               STATUS   ROLES    AGE   VERSION
fargate-ip-10-0-11-6.us-west-2.compute.internal    Ready    <none>   47s   v1.27.6-eks-f8587cb
fargate-ip-10-0-12-72.us-west-2.compute.internal   Ready    <none>   48s   v1.27.6-eks-f8587cb


```


### Fargate profile
- Profile is grouping the resource together



## CI/CD 
### Approach Used In the “Olden” Times

```mermaid

  sequenceDiagram  
    Development -->> Operations: #$@&%*!!!

```
- Development & Operations siloes
- Lack of understanding of the other perspective
- Lack of communication & partnership
- “Fire and forget” engagement


### DevSecOps -- The New Way

```mermaid

  sequenceDiagram
    box rgb(33,69,99) Deployment 
    Development ->> Operations: Deployment
    Operations -->> Development: feedback
    end

```
- Development and operation work together 
- Build of pipeline from developer

### DevSecOps - The Three Ways

```mermaid
---
title: Culture of Continual Experimentation & Learning
---

sequenceDiagram
  Development ->> Operations: Deployment
  loop daily Deployment
    Operations --> Development: feedback
  end

```



Reference: [Source: Gene Kim]('https://itrevolution.com/the-three-ways-principles-underpinning-devops/')

- Culture that fosters:
- Continual experimentation, risk-taking, and learning from failure
- Practice makes perfect!
- Target outcomes:
  - Allocating time for continual improvement (e.g., resolution of technical debt as a standard “category” of work)
  - Taking risks and seeing them pay off - No guts no glory!
  - Expecting failure & building resiliency into the process

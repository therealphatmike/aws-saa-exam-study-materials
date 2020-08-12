# Serverless

## Table of Contents
1. [Lambda Concepts](#lambda-concepts)
4. [Serverless Application Model](#serverless-application-model)
5. [Elastic Container Service](#elastic-container-service)

## Lambda Concepts
* What is lambda?
  * Lambda is the "ultimate abstraction layer"
  * Lambda is a managed compute service that allows you to only worry about your code. It handles all of the underlying infrastructure and scaling for you.
  * Can be used as:
    * An event-driven compute service, like changes in S3 or DynamoDB
    * Compute service run in response to API Gateway call or even AWS SDK calls.
    * Building blocks for workflows built with SWF or Step Functions
  * Lambdas can trigger other lambdas that can trigger lambdas...
* Traditional Architecture vs Serverless Architecture
  * Traditional Architecture
    * User makes a requests
    * Request hits load balancer
    * Load balancer determines which server handles the request
    * Requests gets processed
    * Response sent to user
  * Serverless Architecture
    * Request made to API Gateway or AWS SDK
    * Lambda function triggers and code is executed
    * Response sent to user
* Lambda scales perfectly and instantly -- if 1 million requests made to API Gateway endpoint that triggers a lambda, that lambda will be executed 1 million times. There is no visible load balancer here.
* Lambda supports the following languages natively:
  * JavaScript (Node)
  * Java
  * Python
  * C#
  * Go
  * PowerShell
  * Ruby
* Lambda also provides a runtime that allows you to use any additional programming languages to write your code
* Priced on the number of requests (The first 1 million requests are free, and it's $0.20 per million after that) and the duration of your code executing.
  * The duration is a little more complicated:
    * It is based on GB-seconds. This means you pay based on memory allocated to your function and the time that the function is executing.
    * It costs $0.00001667 for every GB-second
* Again, no syadmins cause there are no servers, continuous scaling, and it's super cheap
  * ACG has about 1 million users and their lambda bill is about $400 per month. To manage their workloads at scale on EC2, their bill would be about $100,000 per month.
* AWS X-Ray allows you to debug your serverless applications

## Serverless Application Model
* AWS SAM is an open source framework that allows you to build serverless applications.
* It is an extension of CloudFormation optimized for serverless apps
* You can define new resources like functions, APIs, tables, and more
* Supports anything CloudFormation supports
* Allows you to run applications locally, which can make testing easier, and your bill cheaper
* Can Package and deploy apps using CodeDeploy 

## Elastic Container Service
* Before we get into ECS, let's talk a little about what containers are:
  * A container is a package that contains an application, libraries, runtime, and other tools required to run the application
  * They are run on a container engine like Docker
  * Provides the isolation benefits of virtualization with less overhead and faster starts than VMs
  * Containerized applications are portable and offer a consistent environment
* So, what is ECS?
  * ECS is a managed container orchestration service
  * Create clusters to manage fleets of container deployments
  * ECS manages EC2 or Fargate instances for you
  * ECS manages your cluster for you including scheduling, running, and monitoring your containers
    * Schedules containers for optimal placement
    * Defines rules for CPU and memory requirements
    * Monitors resource utilization
  * Can run intermittent and persistent jobs in the same cluster
  * Deploy, udpates, roll back
  * FREE!
    * Scheduling, orchestration, and clusters are free, but you do pay for the uderlying EC2 instances or Fargate tasks that you spin up
* There are 6 Basic ECS Components:
  * Cluster - Logical collection of ECS resources -- either ECS EC2 instances or Fargate instances
  * Task - Single running copy of any containers defined by a task definition. One working copy of an application
  * Task Definition - Defines you application. Similar to a Dockerfile but for running containers in ECS. Can contain multiple containers.
  * Service - Allows task definitioins to be scaled by adding tasks. Defines minimum and maximum values.
  * Container Definition - Inside a task definition, it defines the individual containers a task uses. Controls CPU and memory allocation and port mappings.
  * Registry - Storage for container images. (e.g., Elastic Container Registry or DockerHub). Used to download images to create containers.
* What is Fargate?
  * Fargate is a serverless container engine that works with ECS nad EKS (Elastic Kubernetes Service)
  * Eliminates need to provision and manage servers
  * Specify and pay for resources per application
  * Each workload runs its own kernel
    * Provides a high level of isolation and security
* When do I choose EC2 over Fargate?
  * If your application has strict compliance regulations like being able to access the server instances, EC2 may fit the bill better
  * If your application requires a broader level of customization for it's compute resources
  * If you need GPUs
* EKS
  * Elastic Kubernetes Service
    * K8s is an open source software taht lets you deploy and manage containerized applications at scale
  * Allows for the use of the same toolset on-prem and in the cloud
  * Containers are grouped into pods
  * Like ECS, supports both EC2 and Fargate instances
  * Why EKS?  
    * Already using K8s
* ECR
  * Managed Docker container registry in AWS
  * Integrated with both ECS and EKS
  * Can work with your on-prem deployments
  * Highly Available
  * Pay for storage and data transfer
* Load balancing with ECS
  * ECS supports ALB, NLB, and CLB
  * The load balancer will distribute traffic evenly across tasks in your service
  * Use ALB to route HTTPS (layer 7) traffic
    * Recommended over network or classic load balancers
    * Allows for dynamic host port mapping, path-based routing, and priority rules
  * Use NLB or CLB to route TCP (layer 4) traffic
  * Supported by both EC2 and Fargate launch types
* ECS Security:
  * Instance Roles v Task Roles
    * Instance Roles apply a role to all tasks running on a given EC2 Instance
    * Task Roles apply at the task level instead of the instance level, which fits least privilege rules

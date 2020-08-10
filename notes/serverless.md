# Serverless

## Table of Contents
1. [Lambda Concepts](#lambda-concepts)
2. [Building a Serverless Webpage](#building-a-serverless-webpage)
3. [Building an Alexa Skill](#building-an-alexa-skill)
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

## Building a Serverless Webpage

## Building an Alexa Skill

## Serverless Application Model

## Elastic Container Service
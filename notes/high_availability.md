# High Availability Architecture

## Table of Contents
1. [Load Balancer Theory](#load-balancer-theory)
2. [Auto-Scaling Theory](#auto-scaling-theory)
3. [HA Architecture](#ha-architecture)
4. [Cloudformation](#cloudformation)
5. [Elastic Beanstalk](#elastic-beanstalk)
6. [Highly Available Bastions](#highly-available-bastions)
7. [On Premise Strategies](#on-premise-strategies)
8. [Helpful Notes](#helpful-notes)

## Helpful Links
* [What is Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/what-is-load-balancing.html)
* [Registered Instances for you Classic Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-backend-instances.html)

## Load Balancer Theory
* A load balancer is a physical or virtual device that allows you to balance network traffic between multiple webservers
* AWS provides 3 different types of load balancers:
  * Application Load Balancer:
    * Best suited for balancing HTTP and HTTPS loads
    * Operate at Layer 7 and are application aware
    * They are intelligent and you can create advanced request routing, where you can send specific requests to specific servers
  * Network Load Balancer:
    * Best suited for load balancing TCP traffic where extreme performance is required
    * Operates ot Layer 4 (connection layer)
    * Capable of handling millions of requests per second while maintaining ultra low latencies
  * Classic Load Balancer:
    * Legacy load balancers
    * You can load balance HTTP/HTTPS applications and use layer-7 specific features such as X-Forwarded and sticky sessions, but note that these load balancers operate at Layer 4.
    * You can also use classic laod balancers for Layer 4 only applications that communicate over TCP.
    * With classic load balancers, if your application servers stop responding, your load balancer will respond with HTTP 504 - Gateway Timeout. This just indicates that something further downstream than the load balancer is having problems -- it is up to you to figure out what component is broken.
  * Sticky Sessions
    * Classic Load Balancers route each request independently to the registered EC@ instance with the smallest load. Sticky sessions allow you to bind a users session to a specific EC2 instance. This ensures that all requests from that user during the session are sent to the same instance.
    * You can enable sticky sessions for ALBs, but the traffic will be sent at the Target Group Level, not the instance level.
    * Example Scenario:
      * May need to disable sticky sessions if one EC2 instance is not receiving any traffic
      * Enable sticky sessions if you need to write to a local disk (or something else machine-dependent)
  * Cross Zone Load Balancing
    * Let's examine a scenario with no cross zone load balancing: So you have Route53 running DNS for an application and splitting the traffic 50/50 between us-east-1a and us-east-1b. Each AZ has a classic load balancer. us-east-1a has 4 EC2 instances running, and us-east-1b has 1 EC2 instance running. By default your load balancer can't balance load across AZs, so each of your 4 instances in us-east-1a is taking 12.5% of the load and your one instance in us-east-1b is taking 50% of the load.
    * With cross zone load balancing in the above example, all instances will get 20% of the traffic.
    * You don't necessarily need to have a load balancer in the AZ when cross zone load balancing is turned on.
    * This sort of scenario will be in the exam.
  * Path Patterns
    * You can create a listener with rules to forward requests based on the URL path.
    * If you are running microservices, you can route traffic to multiple back-end services using path-based routing.
    * Example scenario:
      * write out from image

## Auto-Scaling Theory
* Auto Scaling has 3 different components:
  * Groups:
    * Logical component -- logical component where you group instances
  * Configuration Templates:
    * Groups use a launch template or a launch configuration as a configuration template for its EC2 instances. You can specify information such as the AMI ID, instance type, key pair, security groups, and block device mapping for your instances.
  * Scaling Options:
    * Parameters that define how your groups scale.
      * Schedule based scaling
      * Option based scaling
    * 5 different scaling options:
      * Maintain current instance levels at all times
        * This configuration maintains a scpecified number of running instances at all time.
        * To maintain current instance levels, AWS uses the health checks you set up, and runs them periodically to make sure the correct number of instances is running.
        * When Auto Scaling finds an unhealthy instance, it terminates that instance and launches a new one.
      * Scale manually
        * Most basic option for resource scaling.
        * You specify the changes manually -- be it by changing the minimum, maximum, or desired capacity.
        * AWS from here will manage the automated launching/termination of instances to meet the new requirements.
      * Scale based on a schedule
        * Scaling actions are performed automatically as a function of date and time.
        * This is useful when you know exactly when you need to scale for load.
      * Scale based on demand
        * Makes use of scaling policies, which let you define parameters that control the scaling process.
        * For example, let's say that you have a web application that currently runs on two instances and you want the CPU utilization of the Auto Scaling group to stay at around 50% when the load on the application changes.
        * This is useful when you know your application will have changing demands for compute capacity, but are unsure when they occur.
      * Use predictive scaling
        * You can use EC2 Auto Scaling in combination with AWS Auto Scaling to scale resources across multiple services.
        * AWS Auto Scaling can help you maintain optimal levels of availability and performance by combining predictive scaling and dynamic scaling (proactive and reactive approaches, respectively) to scale your EC2 capacity faster.

## HA Architecture
* What is High Availability Architecture?
  * HA Architecture is the principles around fault tolerant and resilient systems through architecture.
  * 1st principle: Everything fails. Everything. Always plan for this.
    * Simian Army is a group of projects for testing the resilience of your applciation architectures. (Chaos Monkey, etc)
  * Use multiple AZs and multiple regions wherever you can
  * Know the difference between multi-az and read replicas for RDS
  * Know the difference between scaling out and scaling up:
    * Scaling out: Increase number of instances
    * Scaling up: Increase resources inside instances

## CloudFormation
* A way to script your cloud environment.

## Elastic Beanstalk
* A way of deploying applications to the cloud with one click
* Can work with auto scaling groups, too

## Highly Available Bastions
*  Explained via diagrams

## On Premise Strategies
* On Prem AWS Services:
  * Database Migration Service
    * Allows you to move databases to and from AWS
    * Might have your DR environment in AWS and your on-premises environment as your primary
    * Works with most popular databse technologies, such as Oracle, MySQL, DynamoDB, etc.
  * Server Migration Service
    * Incremental replication of on-prem servers to AWS
  * AWS Application Discovery Service
    * Helps enterprises plan migration projects by gathering information about their on-prem data centers
    * You install the AWS Application Discovery Agentless Connector as avirtual appliance on the VMWare vCenter.
    * It will then build a server utilization map and dependency map of your on-prem env.
    * The collected data is retained in an ecrypted format in an AWS Application Discovery Service data store. You can export this data as a CSV file and use it to estimate the Total Cost of Ownership of running on AWS and to plan your migration to AWS
    * This data is also available in AWS Migration Hub, where you can migrate ythe discovered servers and track their progress as they get migrated to AWS.
  * VM Import/Export
    * Migrate your existing applications to EC2 for DR strategies
  * Download Amazon Linux 2 as an ISO
    * Works with all major virtualization providers

## Helpful Notes
* X-Forwarded-For headers:
  * When you make a request to an application using a load balancer, the load balancer will forward your request to the appropriate server. That request will contain the load balancers internal IP address. This can pose problems if you need to know origin or want IP-related information on your web server *Access Logs will only show the load balancer ip by default).  The X-Forwarded-For header will allow your web servers to know the IP address of the end user who made the request. This is added by Application Load Balancers.

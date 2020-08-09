# Applications

## Table of Contents
1. [SQS](#sqs)
2. [SWF](#swf)
3. [SNS](#sns)
4. [Elastic Transcoder](#elastic-transcoder)
5. [API Gateway](#api-gateway)
6. [Kinesis](#kinesis)
7. [Web Identity Federation](#web-identity-federation)

## SQS
* SQS - Simple Queue Service
* The first service with AWS
* A web service that gives you access to a message queue that can be used to store messages while waiting for a computer to process them. It's a distributed queue system that enables web service applications to quickly and reliably queue messages that one component in a system generates to be consumed by another component.

## SWF
* Simple Work Flow Service
* SWF is a web service that makes it easy to coordinate work across distrubuted application components. SWF is entended to enable applications to take on a variety of use cases as a series of coordinated tasks.
* Tasks are representations of invocations of various processing steps in an application which can be performed by executable code, web service calls, human actions, and scripts.
* SWF is used by Amazon in their warehouses to process and track orders from order time to shipment.
* SWF Actors
  * SWF Workflow Starters - An application that can initiate a workflow. 
  * SWF Deciders - Control the flow of activity tasks in a workflow execution. If something has finished or failed, the decider decides what to do next.
  * SWF Activity Workers - carry out various tasks
* How is SWF different from Step Functions?
  * SWF manages workflow state in the cloud, but the user is responsible for the underlying application infrastructure.
  * Step Functions is a fully managed service that allows you to coordinate distributed applications and microservices using visual workflows. The user is not responsible for the underlying infrastructure. Step Functions allow you to define a workflow as a state machine. Decisions on where to go next are based on what state the machine finds itself in.

## SNS
* SNS is a service that allows you to send notifications from the cloud.
* Easily publish notifications from one application to other applications or end users.
* SNS is a pub-sub type service. Which means there are publishers and subscribers.
* Can easily send notifications by push notifications, SMS, Email, and into SQS queues.
* SNS allows you to group multiple recipients using topics. A topic is an "access point" for allowing ricipients to dynamaically subscribe for identical copies of the same notification. One topic can support delivieries to multiple endpoint types.
* To prevent messages from being lost, all messages published to SNS are stored redundantly across multiple availability zones.
* SNS Benefits:
  * Instantaneous, push-based delivery (no polling)
  * Simple APIs and easy integration with applications
  * Flexible message delivery over multiple transport protocols
  * Inexpenseive, pay as you go model with no upfront costs
  * Webased AWS Management Console offers the simplicity of a point-and-click interface

## Elastic Transcoder
* Elastic transcoder is a media transcoder in the cloud
* Convert media files from their original source format into different formats that will play on smartphones, tablets, PCs, etc
* Provides transoding presets for popular output format, which means that you don't need to guess about which settings work best on particular devices.
* Pay based on the minutes that you transcode and the resolution at which you transcode.

## API Gateway
* API Gateway is a fully managed service that allows developers ao publish, maintain, monitor, and secure APIs at any scale.
* Features and Benefits:
  * Expose HTTPS endpoints to define a RESTful API
  * Serverless-ly connect to services like Lambda and DynamoDB
  * Send each API endpoint to a different target
  * Run efficiently with low cost
  * Scale effortlessly
  * Track and control usage by API key
  * Throttle requests to prevent attacks
  * Connect to CloudWatch to log all requests for monitoring
  * Maintain multiple versions of your API
* How to setup API Gateway:
  * Define an API (container)
  * Define Resources and Nested Resources (URL Paths)
  * For each Resource:
    * Select supported HTTP methods (verbs)
    * Set security
    * Choose target (EC2, Lambda, DynamoDB, etc)
    * Set request and response transformations
* How to deploy API Gateway:
  * Deploy to a stage
    * Uses API Gateway domain by default
    * Can use custome domain
    * Now suports AWS Certificate Manageer: Free SSL/TLS Certs
* You can cache endpoint responses to improve latency and decrease the number of requests made to your API.
  * When you enable caching for a stage, API gateway caches responses from your endpoint for a specified TTL. API Gateway will then respond to the request by looking up the endpoint response from the cache instead of making a request to your endpoint.
* CORS:
  * To understand how API Gateway works with CORS, we need to understand the same-origin policy: A web browser permits scripts contained in a first web page to access data in a second web page, but only if both web pages have the same origin. This is done to prevent XSS attacks.
    * While this is enforced by web browsers, it is important to note that tools like cUrl and Postman ignore the same-origin policy.
  * CORS: Cross Origin Resource Sharing - is a mechanism that allows restricted resources on a web page requested from another domain outside the domain from which the first resource was served.
  * How does CORS work?
    * Browser makes an HTTP Options call for a URL (OPTIONS is an HTTP method like GET, PUT, and POST)
    * Server returns a response that says: "these other domains are approved to GET this URL."
    * Error - "Origin policy cannot be read at the remote resource" This would mean that you need to enable CORS on API Gateway.

## Kinesis
* What is streaming data?
  * Streaming data is data that is generated continuously potentially by thousands of data sources. These data sources send in data records simultaneously and in small sizes (kilobytes)
* What is Kinesis?
  * Kinesis is a platform on AWS to send your streaming data to. It makes it easy to load and analyze streaming data, and also provides the ability for you to build your own custom aplications for your business needs.
* 3 Different Kinesis Flavors:  
  * Kinesis Streams:
    * Streams are a place to allow producers to send data to be temporarily stored in shards until a consumer can process the data
    * Shards are groups of data on a Kinesis data stream. They consist of up to 5 transactions per second for reads, up to a maximum total data read rate of 2 MB per second and up to 1,000 records per second for writes, up to a maximum toal data write rate of 1MB per second (including partition keys).
    * The data capacity for your stream is a function of the number of shards that you specify for the stream. The total capacityof the stream is the sum of the capacities of its shards.
    * Data stored for a default of 24 hours, but can be changed to store for up to 7 days.
  * Kinesis Firehose
    * Producers send data to firehose, but unlike a Kinesis Stream, there is no persistence. The data has to be processed as it comes in. Generally, it will immediately be dumped into some form of data store.
  * Kinesis Analytics
    * Works with Kinesis Streams or Kinesis Firehose.
    * Allows you to perform analytics on the fly on data coming in, and storing the results in a storage service.

## Web Identity Federation
* Web Identity Federation lets you give users access to AWS resources after they have successfully authenticated with a web-based identity provider like Amazon, Facebook, or Google. Following successful authentication, the user receives an authentication code from the IDP, which they then trade for temporary AWS security credentials.
* The service that provides Identity Federation is AWS Cognito. It has the following features:
  * Sign up and sign in to your apps.
  * Add access for guest users.
  * Acts as an Identity Broker between your application and IDPs, so you don't have to write that code.
  * Synchronizes your user data for multiple devices.
* Cognito brokers between your app and an IDP to provide temporary credentials which map to an IAM role allowing access to the required resources.
* No need for the application to embed or store AWS credentials locally on the devide and it gives users a seamless experience across all mobile devices.
* Cognito works with either User Pools or Identity Pools.
* User Pools:
  * User pools are user directories used to manage sign-up and sign-in functionality for mobile and web applications.
  * Users can sign-in directly or use an external IDP.
  * Cognito acts as an Identity Broker between the identity provider and AWS.
  * Successful authentication generates a JSON web token (JWT -- pronounced JOT)
* Identity Pools:
  * Provide temporary AWS credentials to access AWS services like S3 or DynamoDB
* User Pools are for managing users, Identity pools allow you to grant resources to AWS Resources.
* Cognito tracks the association between user identity and the various different devices they sign in from. In order to provide a seamless experience for your application, Cognito uses Push Synchronization to push updates and synchronize user data across multiple devices. Cognito uses SNS to send a notification to all the devices associated with a given user identity whenever data stored in the cloud changes.
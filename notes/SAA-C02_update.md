# SAA-C02 Update

This update is for all sections up to Databases on the ACG Udemy course.

## Table of contents
1. [IAM and S3](#iam-and-s3)
2. [EC2](#ec2)
3. [Databases](#databases)

## IAM and S3
* S3 Lock Policies and Glacier Vault Lock
  * S3 Object Lock allows you to store object using a WORM model (Write Once Read Many). It allows you to set up time frames in which objects cannot be modified or deleted. This can be very helpful for regulatory requirements.
    * In Governance Mode, users can't overwrite or delete an object version or alter is lock setttings unless they have special permissions. This implies that there is a set of permissions that allows users to alter these settings or delete the object.
    * In Compliance Mode, a protected object version can't be overwritten or ddeleted by a user, including the root user in your AWS account. When an object is locked in compliance mode, its retention period can't be shortenedand its retention mode can't be changed.
    * A Retention Period protects an object version for a fixed amount of time. After the retention period expires, the object version can be overwritten or deleted unless you also placed a legal hold on the object version.
    * A Legal Hold is like a retention period, but without an associated tiem frame. A legal hold remains in effect until it is removed. Legal holds can be freely placed and removed by any user who has the s3:PutObjectLegalHold permission.
  * Glacier Vault Lock allows you to easily deploy and enforce compliance controls for individual S3  Glacier vaults with a Vault Lock Policy. You can specify controls, such as WORM, in a Vault Lock Policy and lock the policy from future edits. Once locked, the policy can no longer be changed.
* S3 Performance
  * S3 Prefixes:
    * A prefix is the file path between the bucket name and the object name.
    * Prefixes are important for S3 Performance because request limits are based on prefixes. You can achieve 3,500 PUT/COPY/POST/DELETE and 5,500 GET/HEAD requests per second per prefix. SO if you have objects that need high levels of availability, you can spread them across multiple prefixes.
    * S3 also has a different limit set when using SSE-KMS to encrypt/decrypt objects. In this scenario, you are limited to the KMS limitations.
      * On upload, you will call GenerateDataKey from the KMS API.
      * On download, you will call Decrypt in the KMS API.
      * This becomes important, especially depending on the region. Uploading/Downloading will count toward the KMS quota. Depending on the region, it is either 5,500, 10,000, or 30,000 requests per second.
      * Currently, you cannot request a quota increase for KMS.
    * Another way to increase S3 performance is to use MultiPart uploads. It is recommended for files over 100MB, and required for files over 5GB. Essentially allows you to parallelize your uploads.
    * S3 Byte-Range Fetches are essentially multi-part uploads, but for downloads. Allows failures to be isolated to a specific byte range. Can also be used to downloadload partials bits of a file like meta data. 
* S3 Select and Glacier Select
  * Enables appllications to retrieve only a subset of data from an object by using SQL Expressions. By using S3 Select to retrieve only the data needed by your application, you can achieve drastic performance increases. The potential for cost savings also exists by using S3 Select. This not only improves your S3 performance, but also will improve the performance of your underlying application.
  * Galcier select is just like S3 Select that allows you to query data from Glacier directly.
* DataSync Overview
  * Put simply, DataSync will allow you to sync data between AWS and an On-Prem Data Center. Automatically enrypts data and accelerates transfer over the WAN. DataSync performs automatic data integrity checks in-transit and at-rest. Works with S3, EFS, and FSx.
* CloudFront Signed URLs and Cookies
  * A way to restrict content on a given platform.
  * This can be done through Signed URLs or Signed Cookies
    * A signed URL would be used for individual files.
    * A signed cookie is for multiple files.
    * When we create a signed URL or signed cookie, we attach a policy. The polikcy can include URL expiration, IP Ranges, and Trusted signers.
    * How do signed URLs work?
      * OAI: Origin Access Identity. Users can't access the S3 bucket directly. Instead, they access CloudFront which accesses S3 using OAI. The user would log into an application, and once authorized/authenticated, they would generate a signed URL through the CloudFront SDK. The client would then use the URL to access the content that is protected.

## EC2
* Spot Instances and Spot Fleets
  * Spot instance requests can eb one time or persistent.
  * Spot fleets are groups of spot instances, but may also include regular, on demand instances to meet target capacity.
    * Spot fleets will try to match your target capacity with your price constraints.
    * Spot fleets can be spun up with the following strategies:
      * capacityOptimized - instances come from the pool with optimal capacity for the number of instances launching
      * lowestPrice - instances come from the pool with the lowerst price. This is the default strategy
      * diversified - instances are distributed across pools
      * InstancePoolsToUseCount - instances are distributesd across the number of spot instance pools you specify. This parameter is valid only when used in combination with lowestPrice.
* EC2 Hibernate
  * When you hibernate an EC2 instance, the operating system is told to perform hibernation (suspend-to-disk). Hibernation saves the contents from the instance memory (RAM) to your Amazon EBS root volume. We persist the instance's Amazon EBS root volume and any attached Amazon EBS data volumes.
  * Restored to previous state, ram loaded, old processes resumed, previously attached data volumes are reattached and the instance retains its instance id.
  * To use hibernation you have to encrypt the root volume and make sure it's large enough to store the RAM.
  * Decreases spin up time.
  * Instances can't be hibernated for more than 60 days
  * Available for Windows, Amazon Linux 2, and Ubuntu AMIs
* HPC on AWS
  * Achievable through Data Transfer services, compute and networking services, storage services, and then orchestration and automation services to help pull it all together.
  * AWS Batch - Run hundreds of thousands of batch computing jobs on AWS
    * AWS Batch supports multi-node lparallel jobs, which allows you to run a single job that spans multiple EC2 instances
    * You can easily schedule jobs and launch EC2 instances according to your needs
  * AWS Parallel Cluster - Open-source cluster management tool that makes it easy to deploy and manage HPC clusters on AWS
    * ParallelCluster uses a smimple text file to model and provision all the resources needed for your HPC applications in an automated and secure manner
    * Automate the creation of VPC, subnet, cluster type, and instance types.

## Databases
* Advanced DynamoDB
  * DyanmoDB Accelerator (DAX) - A fully manages, highly available, in-memory cache
    * Up to 10x performance improvement
    * Reduces request time from imlliseconds to microseconds -- even under load
    * No need to manage caching logic as it is fully integrated and compatible with existing DynamoDB API calls
    * Integrated HA architecture
  * DynamoDB transactions
    * Two underlying reads/writes -- prepare and commit
      * This means DynamoDB Transactions will eat up more of your capacity
    * Up to 25 items or 4MB of data at a time
  * On Demand Capacity
    * Pay-Per-request pricing
    * Balance of cost and performance
    * No minimum capacity
    * No charge for read/write when table is idle -- just for storage and backups
    * Pay more per request than wqith provisioned capacity
    * Great for new product launches until product reaches a steady state
  * On Demand Back Up and Restore
    * Unlike tradition RDBMSs, these will have zero impact on table performance and availability
    * Consistent within seconds and retained until deleted
    * Operates within the same region as the source table
    * Point In Time recovery -- restore data to any point in time in the last 35 days
  * Streams -- Time-ordered sequence of item level changes in a table
    * Stream data is stored for 24 hours and contains inserts, updates, and deletes
    * Can be used for cross region replication or establish relationships across tables with stream, analytics, etc
    * Combine streams with lambda functions to get functionality like stored procedures in traditioonal databases
  * Global Tables -- Multi-Master, Multi-Region Replication
    * Great for globally distributed applications
    * Based on DynamoDB streams
    * Multi-Region redundancy for DR or HA
    * No application rewrites
    * Replication latency under one second across regions
  * Database Migration Service (DMS)
    * Migrates source database to target database, can be cloud-to-cloud, on-prem to on-prem, or any mixture mixture of the two.
    * Logic is configurable in DMS
    * Source database will remain fully operational
    * DyanoDB is not a valid source database, but is a valid target database
  * Security
    * All data is encrypted at rest using KMS
    * You can also use site-to-site VPN, Direct Connect, IAM to control access
* Database Migration Service
  * Types of migrations:
    * Homogeneous migrations -- on prem oracle to cloud oracle
    * Heterogeneous migrations -- on prem mySQL to cloud Aurora
    * Can even migrate data from S3 to a database
    * AWS SCT:
      * Schema Conversion Tool is used for migrating one RDBMS flavor to another, for example Oracle to AWS Aurora
      * Not needed if migrating to an identical database
* Caching Strategies on AWS
  * Which services have caching capabilities:
    * CloudFront
    * API Gateway
    * ElastiCache -- duh
    * DynamoDB Accelerator (DAX)
  * Remember that caching is a balancing act between up-to-date, accurate information and latency
* EMR Overview
  * Elastic Map Reduce
  * EMR is the industry-leading cloud big data platform. With EMR you can run petabyte scale analysis at a cheaper rate and fast than with traditional Apache Spark.
  * The central component of Amazon EMR is the cluster. A cluster is a collection of Amazon EC2 instances. Each instance in the cluster is a called a node. Each node has a role within the cluster, referred to as the node type.
    * Amazon EMR also installs different software components on each node type, giving each node a role in a distributed application like Apache Hadoop.
  * Node Types:
    * Master Node - A node that manages the cluster. The maste node tracks the status of tasks and monitors the health of the cluster. Every cluster has a master node.
    * Core Node - A node with software components that runs tasks and stores data in the Hadoop Distributed File System (HDFS) on your cluster. Multi-node clusters have at least one core node.
    * Task Node - A node with software components that only run tasks, and doesn't store data in HDFS. Task nodes are optional.
  * All nodes can communicate with each other.
  * Logs are stored on the master node at /mnt/var/log
    * This means if you lose your master node, you lose your logs
  * You can configure a cluster to periodically archive the log files stored on the master node to Amazon S3. This ensures the log files are available after the cluster terminates, whether this is through normal shutdown or due to an error. Amazon EMR archives the log files to Amazon S3 at 5 minute intervals.
    * You can only set this up when you first set up the cluster.

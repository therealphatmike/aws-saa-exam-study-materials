# Advanced IAM

## Table of Contents
1. [AWS Directory Service](#aws-directory-service)
2. [IAM Policies](#iam-policies)
3. [Resource Access Manager](#resource-access-manager) 
4. [AWS Single Sign On](#aws-single-sign-on)

## AWS Directory Service
* It is a family of managed services that allows you to connect your on premises AD to AWS resources
* It is a standalone directory in the cloud
* Use existing corporate credentials
* SSO to any domain-jointed EC2 instance
* What is Active Directory?
  * On-prem directory service
  * Hierarchical database of users, groups, computers -- trees, and forests
  * Group Policies
  * LDAP (Lightweight Directory Access Protocol) and DNS
  * Kerberos, LDAP, NTLM Authentication protocols
  * Intended to be configured in an HA architecture
* AWS Managed Microsoft AD
  * AD Domain Controllers running windows servers
  * Reachable by applications in your VPC
  * Add DCs for HA and performance
  * Exclusive access to DCs
  * Extend existing AD to on-premises using AD Trust
  * AWS Managed:
    * Multi-AZ deployment
    * Patch, Monitor, Recover
    * Instance Rotation
    * Snapshot and restore
  * Customer Managed:
    * Users, groups, GPOs
    * Standard AD tools
    * Scale out DCs
    * Trusts (resource forest)
    * Certificate Authorities (LDAPS)
    * Federation
* Simple AD:
  * Standalone managed directory
  * Basic AD Features
  * Use a small Simple AD for less than 500 users, or a large for up to 5,000 users
  * Easier to manage EC2 instances
  * Linux workloads that need LDAP
  * Does not support trusts
* AD Connector
  * When you want to use your on-prem AD for AWS resources
  * Avoid caching information in the cloud
  * Allow on-prem users to log in to AWS using AD
  * Join EC2 instances to your existing AD domain
  * Scale across multiple AD Connectors
* Cloud Directory
  * Directory-based store for developers
  * Multiple hierarchies with hundreds of millions of objects
  * Use cases: org charts, course catalogs, device registries
  * Fully managed service
* Amazon Cognito User Pools
  * Managed user directory for SaaS Applications
  * Enalbes signup/sign in with web and mobile
  * Works with social media identities
* AD Compatible:
  * Managed Microsoft AD
  * AD Connector
  * Simple AD
* Not AD Compatible:
  * Cloud Directory
  * Cognito User Pools

## IAM Policies
* Amazon Resource Name
  * ARN for short.
  * Uniquely identify any resource in AWS
  * Begins with: arn:partition:service:region:account_id:
    * partitions: AWS | AWS-CN
  * Ends with: resource or resource type, can also be followed by a qualifier
  * Examples:
    * arn:aws:iam::123456789012:user/mark
    * arn:aws:s3:::my_awesome_bucket/image.png
    * arn:aws:dynamodb:us-east-1:123456789012:table/orders
* IAM Policies:
  * JSON Document that defines permissions
  * Identity Policies: Attached to a user, group, or role.
  * Resource Policies: Attached to AWS resources like S3 buckets. You can specify who/what has access to the resource or what actions that entity can take.
  * No effect until attached to an identity or resource.
  * Just a list of statements. Each statement matches an AWS API Request
  * Example:
    ```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Sid": "SpecificTable",
          "Effect": "Allow",
          "Action": [
            "dynamodb:BatchGet*",
            "dynamodb:DescribeStream",
            "dynamodb:Get*"
          ],
          "Resource": "arn:aws:dynamodb:*:*:table/MyTable"
        }
      ]
    }
    ```
  * Sid is just a human readable string so you know what the statement is for
  * Effect can be either Allow or Deny
  * Matched based on their Action
  * Resource is the resource that the actions can be taken against
  * A permission that is not explicitly allowed means it is implicitly denied
  * An explicit deny trumps anything else
  * AWS will join all applicable policies
* Permission Boundaries
  * Used to delefate administration to other users
  * Helps prevent privilege escalation or unecessarily broad permissions
  * Control the maximum permissions an IAM policy can grant

## Resource Access Manager
* AWS Resource Access Manager (RAM) allows resource sharing between accounts.
* Resources you can currently share via RAM:
  * App Mesh
  * Aurora
  * CodeBuild
  * EC2
  * EC2 Image Builder
  * License Manager
  * Resource Groups
  * Route 53

## AWS Single Sign On
* Allows for granular account-level permissions
* AD and SAML integration
* SAML - Security Assertion Markup Language
* All sign on activity is arecorded in cloudtrail 

# Advanced IAM

## Table of Contents
1. [AWS Directory Service](#aws-directory-service)
2. [IAM Policies](#iam-policies)
3. [AWS Single Sign On](#aws-single-sign-on)

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

## AWS Single Sign On


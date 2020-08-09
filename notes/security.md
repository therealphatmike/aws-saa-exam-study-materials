# Security

## Table of Contents
1. [Reducing Security Threats](#reducing-security-threats)
2. [KMS](#kms)
3. [Cloud HSM](#cloud-hsm)
4. [Parameter Store](#parameter-store)

## Reducing Security Threats
* Bad Actors:
  * This is not Nicholas Cage.
  * Bad Actors are typically automated processes such as content scrapers, bad bots, fake user agents, DoS attackers, etc that use your applications in an unintended or harmful way. 
  * Not serving content to bad actors can reduce costs by lowering traffic
  * We can achieve this in the following ways (not necessarily a comprehensive list):
    * Use a NACL to block traffic from the IPs of known bad actors.
    * Use a host-based firewall to restrict traffic.
  * ALBs can complicate this a little bit
    * Connections from bad actors are terminated at the load balancer
      * This means your EC2 instances are not user IP aware, so a Host-based firewall would be ineffective here.
    * Another thing that can be done here is to allow ONLY the ALBs security group permission to the EC2 instance to prevent unwanted traffic to that instance. 
    * This won't block traffic to that ALB.
    * Another option is to use AWS WAF attached to your ALB.
      * Lets your specify the block/allow traffic based on different constraints.
      * Remember WAF operates on layer 7, so you can do things like inspect a request for malicious scripts or SQL injection attacks, and block or allow accordingly.
  * With NLBs, you have to use a NACL to block a bad actors IP address from creating traffic on your servers.
  * You can also attach WAF to a CloudFront distro, and use the IP blocking and filtering options, or use GeoMatch to block entire regions. This will terminate a bad actors IP address at CLoudFront, so it won't even make it to your NACL.

## KMS
* Key Management Service: A regional secure key management, encryption, and decrytpion service.
* Manages Customer Master Keys (CMKs)
  * A CMK is a logical representation of a key. The key never leaves the region (or AWS)
* Ecrypt and Decrypt data up to 4KB
* Pay per API call
* Audit capability using CloudTrail to satisfy regulatory requirements. Audit logs are delivered to S3
* KMS is a FIPS 140-2 Level 2 service.
  * Level 2 means you have to show evidence of tampering.
  * CloudHSM is Level 3 if you need more strict requirements.
* 3 Types of CMKs:
  * AWS Managed CMK - Free; used by default if you pick ecryption in most AWS services. Only that service can use them directly.
  * Customer Managed CMK - Allows key rotation; controlled via key policies and can be enabled/disabled. Full control over lifecycle.
  * AWS Owned CMK - Used by AWS on a shared basis across many accounts; you typically won't see these.
* Symmetric vs. Asymmetric CMKs:
  * Symmetric:
    * Same key used for encryption and decryption
    * AES-256
    * Data never leaves AWS unencrypted
    * You are responsible for calling KMS APIs to use symmetric keys
    * AWS Services integrated with KMS use symmetric CMKs
    * Encrypt, decrypt, and re-encrypt data
    * Generate data keys, data key pairs, and random byte strings
    * Import your own key material
  * Asymmetric:
    * Mathematically related public/private key pair
    * RSA and elliptic-curve cryptography(ECC) based
      * ECC is newer and considered more secure
    * Private key never leaves AWS unecnrypted
    * Must call the KMS APIs to use private key
    * Download the public key and use outside of AWS
    * Used oautside AWS by users who can't call KMS APIs 
    * AWS services integrated with KMS _do not support_  asymmetric CMKs
    * Sign messages and verify signatures
    * This is the type of encryaption SSL certificates user
* If you don't provide a Key Policy when your create a KMS Key, AWS will give you the default policy which will grant root user full access to the CMK, and enables IAM policies in the AWS account to grant full access to the CMK:
  ```json
  {
    "Sid": "Enable IAM User Permissions",
    "Effect": "Allow",
    "Prinicpal": {
      "AWS": "arn:aws:iam::111122223333:root"
    },
    "Action": "kms:*",
    "Resource": "*"
  }
  ```
* AWS managed keys will have an auto-generated alias in the console
* Remeber that CMKs are regional
* Aliases can help swap keys without changing code
* To encrypt files larger than 4KB we need to use a Data Encryption Key

## Cloud HSM
* HSM - Hardware Security Modules
* CloudHSM is the AWS solution for highly secure regulatory scenarios
* FIPS 140-2 Level 3
  * Level 3 is a security level that involves physical devices that are designed to zero out all plaintext security provider data inside when a certain physical breech level is reached.
* (Remember KMS is level 2)
* With CloudHSM, you own your own keys
* CloudHSM offers a single-tenant, dedicated hardware, multi-AZ cluster dedicated to you
* No access to AWS-Managed component. You control your access keys and AWS doesn't have access to your keys
* Runs within a VPC in your account
* Runs on industry standard APIs, AWS _does not_ have their own APIs for this
* PKCS#11, JCE, CNC compatible
* KEEP YOUR KEYS SAFE. If you lose your keys, they are unretreivable.
* HSMs are _NOT_ Highly Available by default

## Parameter Store
* A tool for cachign and distributing secrets securely to AWS resources
* A component of AWS SSM (Systems Manager)
* Secure, serverless storage ofr configuration and secrets:
  * Passwords
  * Database connection strings
  * License Codes
  * API Keys
* Values can be stored encrypted (KMS) or plaintext
* Separate data from source control to increase application security
* Parameters can be stored in hierarchies
  * Hierarchies up to 15 levels deep
  * Hierarchies also make permissions more granular
  * Parameters can be retrieved by path
* Parameters can also have versions
* TTLs also enforce password rotation
* Parameter store is very well integrated with CloudFormation

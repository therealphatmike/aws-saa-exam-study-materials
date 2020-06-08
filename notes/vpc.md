# VPCs -- Virtual Private Clouds

VPCs are immensly important to the Solutions Architect. Apparently, if you can build a VPC from memory by the time you take the exam, you should be able to pass.

## Table of Contents
1. [Useful Links](#useful-links)
2. [Introduction](#introduction)
3. [Building Our Own VPC](#building-our-own-vpc)
4. [NAT Instances and NAT Gateways](#nat-instances-and-nat-gateways)
5. [Access Control Lists](#access-control-lists)
6. [VPC Flow Logs](#vpc-flow-logs)
7. [Bastion Hosts](#bastion-hosts)
8. [Direct Connect](#direct-connect)
9. [Global Accelerator](#global-accelerator)
10. [VPC Endpoints](#vpc-endpoints)

# Useful Links
* [VPC FAQs](https://aws.amazon.com/vpc/faqs/)
* [NAT Instance Basics](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html#basics)
* [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)
* [Configureing a VPN Over Direct Connect](https://www.youtube.com/watch?v=dhpTTT6V1So&feature=youtu.be)
* [Global Accelerator](https://aws.amazon.com/global-accelerator/?blogs-global-accelerator.sort-by=item.additionalFields.createdDate&blogs-global-accelerator.sort-order=desc&aws-global-accelerator-wn.sort-by=item.additionalFields.postDateTime&aws-global-accelerator-wn.sort-order=desc)

# Introduction
* A VPC can be thought of as a virtual data center in the cloud.
* Amazon lets you provision a logically isolated sections of the AWS cloud where you can launch AWS resources in a virtual network that you define.
* You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways.
* You can easily customize the network config for your VPC
  * You can create a public-facing subnet that your application servers that have access to the Internet. Then you could have another subnet that your backend servers or databases live in that doesn't have public-facing Internet access to keep your sensitive data systems more secure.
* You can leverage multiple layers of security, including security groups and network access control lists, to help control access to EC2 instances in each subnet.
* You can create a VPN connection between your corporate data center and your VPC to leverage the AWS cloud as an extension of your corporate data center.
* Internet Assigned Numebrs Authority has 3 different sets of IPs that are reserved for private IP address ranges:
  * 10.0.0.0 - 10.255.255.255 (10/8 prefix)
  * 172.16.0.0 - 172.31.255.255 (172.16/12 prefix)
  * 192.168.0.0 - 192.168.255.255 (192.168/16 prefix)
* Playing around on [Cidr.xyz](https://cidr.xyz/) is a good way to start understanding how CIDR ranges work, and how the `0.0.0.0/0` notation affects available IP addresses in the range.
* What can we do with a VPC?
  * Launch instances int oa subnet of your choosing
  * Assign custom IP address ranges in each subnet
  * Configure route tables between subents
  * Create internet gateway and attach it to our VPC
  * Much better security control over your AWS resources
  * Instance security groups
  * Subnet network access control lists
* Default VPC vs Custom VPC
  * Default is user firendly -- it allows you to immediately deploy instance. This is intended to be super developer friendly.
  * All subnets in default VPC have a route out to the internet
  * Each EC2 instance has a public and private IP address
  * If you elete a default VPC, you can recover it
* VPC peering allows you to connect VPC to another via a direct netowrk route using private IP addresses
  * Instances behave as if they are on the same private network
  * You can peer VPCs with other AWS accounts as well as with other VPCs in the same account.
  * Peering is always in a star configuration.
    * 1 central VPC peers with 4 others (no transitive peering)
    * Peering across regions is allowed
* 1 subnet = 1 AZ
  * SN - AZ is a many to one relationship, meaning you cannot span multiple AZs with a subnet, but you can put multiple subnets in an AZ
* Security Groups are stateful whereas NACLs are stateless
  * NACLs allow allow AND deny rules.
  * You have to create outbound rules for correlated inbound rules in a NACL

# Building Our Own VPC
* Create VPC from 'Your VPCs' creates a VPC, a new Route Table, a new NACL, and a new security group. You don't get new subnets or internet gateways, though
* You always get n-5 available IPs, where n is the number of IPs available based on your cidr netmask, when creating a subnet because amazon reserves 5 IP addresses.
* You can only have 1 internet gateway per VPC
* AZs are randomized
* Security Groups cannot span VPCs

# NAT Instances and NAT Gateways
* NAT - Network Address Translation
* We want to be able to enable EC2 instances in private subnets to download software. We want to do this in a manner that keeps our subnet private.
* NAT Instances are 1 EC2 instance that acts as a NAT Gateway
  * Instances are being deprecated, but we still need to understand them
* NAT Gateways are a HA implementation of NAT Instances
* Each EC2 instance performs source/destination checks by default. This means that the instance must be the source or destination of any traffic it sends or receives. However, a NAT instance must be able to send and receive traffic when the source or destination is not itself. Therefore, you must disable source/destination checks on the NAT instance.
  * You can then create a new route in your route table that lets your instances on your private subnet communicate out through your NAT instance.
* NAT instance needs to be in a public subnnet with a route from a private subnet to the public to allow instnaces in the private subnet to communicate with the NAT Instance
* If you are bottlenecking, just increase the size
* You can create your own HA with NAT instances, but it's a pain, so you may as well use NAT Gateways
* NAT Gateways are redundant inside the AZ
* Only one NAT GW per AZ
* NAT stats with 5Gbps and scales to 45Gbps
* No need to patch OS for NAT GW
* If you have resources in multiple AZs and they share one NAT gateway, in the envent that the NAT GWs AZ is down, resources in the other AZs lose internet access.
  * To create an AZ-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT GW in the same AZ

# Access Control Lists
* Whenever you create a new NACL, all inbound and outbound traffic will be denied by default
* Ephemeral ports:
  * 
* Evaluates rules in order of rule number -- with the default deny everything else going last
* Each time you add a subnet to a vpc, it is assigned to the default NACL.
  * You can change the NACL that your subnet is associated with, but a subnet can be associated with only 1 NACL
  * Obviosuly, each NACL can handle multiple subnets
* NACLs act before security groups (remember the diagrams)

# VPC Flow Logs
* VPC Flow logs allow you to capture information about the IP traffic going to and from network interfaces in your VPC. 
  * Flow log data is stored using CloudWatch Logs.
  * After you've created a flow log, you can ciew and retrieve its data in CloudWatch
* Flow logs can be created at 3 different levels:
  * VPC
  * Subnet
  * ENI
* Cannot enbale flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account
* You can tag flow logs
* After you create a flow log, you cannot change its configuration: for example, you can't associate a different IAM role with the flow log
* Not all traffic is monitored:
  * Traffic generated by instances when they contact the Amazon DNS server is not logged. -- if you use your own DNS server, then all traffic to that DNS server is logged.
  * Traffic generated by a Windows instance for Amazon Windows License activation is not monitored.
  * Traffic to and from 169.254.169.254 traffic is not monitored
  * DHCP Traffic is not monitored
  * Traffic to the reserved IP address for the default VPC rotuer

# Bastion Hosts
* What is a bastion host?
  * A special purpose computer on a network specifically designed and configured to withstand attacks. The computer generally hosts a single application, for example a proxy server, and all other services are removed or limited to reduce the threat to the computer. It is hardened in this manner primarily due to its location and purpose, which is either on the outside of a firewall or in a demilitarized zone and usually involves access from untrusted networks or computers.
* A NAT Gateway is used to provide internet traffic to EC2 in a private subnet
* A bastion is used to securely adminiter EC2 instances (Using SSH or RDP). Bastioned are also called Jump Boxes
* You cannot use a NAT gateway as a Bastion host.

# Direct Connect
* Direct Connect is a cloud service taht makes it easy to establish a dedicated network connection from your premises to AWS.
  * Using Direct Connect, you can establish private connectivity between AWS and your datacenter, office, or colocation environment which can potentially reduce your network costs, increase bandwidth/throughput, and provide a more consistent network experience than internet-based connections.
* Useful for high throughput workloads (lots of network traffic) or if you need a stable, reliable, and secure conenction.

# Setting Up a VPN Over Direct Connect
* This will be a theoretical exercise as DX connections will definitely take you out of free tier. These steps are important because you will be tested on this.
1. Create a virtual interface in the Direct Connect console. This is a **PUBLIC Virtual Interface**!
2. Go to the VPC console and then to VPN connections Create a customer gateway.
3. Create a Virtual Private Gateway.
4. Attach teh Virtual Private Gateway to the desired VPC.
5. Select VPN Connections and create a new VPN connection.
6. Select the Virtual Private Gateway and the Customer Gateway.
7. Once the VPN is available, setup the VPN on the customer gateway or firewall.

# Global Accelerator
* Global Accelerator is a service in which you create accelerators to improve availability and performance of your applications for local and global audiences.
* Glboal Accelerator works by directing traffic to optimal endpoints over the AWS global network. This improves the availability and performance of your internet application that are used by a global audience.
* By default Global Accelerator provides you with two static IP addresses taht you associate with your accelerator.
  * You can bring your own IPs if you want.
* Global Accelerator includes the following components:
  * Static IP Addresses
  * Accelerator
    * Accelerators direct traffic to optimal endpoints over the AWS global network to improve performance of your applications.
    * Each accelerator has one or more listeners.
  * DNS Name
    * Each accelerator gets assigned a default DNS Name (something like 1234234sdsdfsdf.awsglobaleccelerator.com) that points to the static IP addresses that Global Accelerator assigns to you.
    * Depending on the use case, you can use your accelerator's iP addresses or DNS name to route traffic to your accelerator, or setup DNS records to route traffic using your own custom domain name.
  * Network Zone
    * Services the static IP addresses for your accelertrator from a unqiue IP subnet.
    * Similar to an AZ, a network zone is an isolated unit with its own set of physical infrastructure.
    * When you configure an accelerator, by default, global accelerator allocates two IPv4 addresses for it. If one IP addres from a network zone becomes unavailable due to IP address blocking by certain client networks, or network disruptions, client applications can retry on the healthy static IP address from the other isolated network zone.
  * Listener
    * Processes inbound connection from clients to Global Accelerator, based on the port (or port range) and protocol that you configure.
      * Global Accelerator supports both TCP and UDP protocols.
    * Each listener has one or more endpoint groups associated with it, and traffic is forwarded to endpoints in one of the groups.
    * You associate endpoint groups with listeners by specifying the regions that you want to distribute traffic to. Traffic is distributed to optimal endpoints within the endpoint groups associated with a listener.
  * Endpoint Group
    * Each endpoint is associated with a specific AWS region
    * Endpoint groups include one or more endpoints in the region
    * You can increase or reduce the percentage of traffic that would be otherwise directed to an endpoint group by adjusting a setting called the traffic dial.
    * The traffic dial lets you easily do performance testing or bluie/green deployment testing for new releases across different AWS Regions.
  * Endpoint
    * Can be things like Network Load Balancers, ALBs, EC2 instances, or Elastic IP addresses.
    * An ALB endpoint can be internet-facing or internal.
    * Traffic is routed to endpoints based on configuration options that you choose, such as endpoint weights.
    * For each endpoint, you can configure weghts, which are numbers taht you can use to specify the proportion of traffic to route to each one. This can be useful, for example, to do performance testing within a region.

# VPC Endpoints
* Enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT Device, VPN connection, or AWS Direct Connect connection. 
* Instances in your VPC do not require public IP addresses to communicate with resources in the service. 
* Traffic betwween your VPC and the other services does not leave the AWS network.
* Endpoints are virtual devices that scale horizontally, are redundant and highly available, and allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic.
* Two types of VPC endpoints:
  * Interface Endpoints
    * An interface endpoint is an elastic network interface with a private IP address that serves as an entry point for traffic destined to a supported service
  * Gateway Endpoints
    * Essentially NAT Gateways. Support S3 and DynamoDB

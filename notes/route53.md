# Route 53

## Table of Contents
1. [DNS 101](#dns-101)
2. [Simple Routing](#simple-routing-policy)
3. [Weighted Routing Policy](#weighted-routing-policy)
4. [Latency Based Routing Policy](#latency-based-routing-policy)
5. [Failover Routing Policy](#failover-routing-policy)
6. [Geolocation Routing Policy](#geolocation-routing-policy)
7. [Geoproximity Routing Policy](#geoproximity-routing)
8. [Multivalue Answer Routing Policy](#multivalue-answer-routing-policy)
9. [Summary](#summary)

## Useful Links
* [How Health Checks Work in Complex Route 53 Configurations](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-complex-configs.html)

# DNS 101
* Route 53 is a throwback to route 66, but it's 53 because DNS runs on port 53
* What is DNS?
    * Domain Name System - a hierarchical and decentralized naming system for computers, services, or other resources connected to the internet or a private network. It associates various information with domain names assigned to each of the participating entities.
    * DNS is used to convert human friendly domain names to IPv4 or IPv6 addresses.
    * IPv4 vs IPv6:
        * IPv4 space is a 32*bit field and has over 4 billion different addresses (4,294,967,296 to be precise).
        * IPv6 was created to solve the depletion of IPv4 addresses and has an address space of 128*bits which, in theory is 340,282,366,920,938,463,463,372,607,431,768,211,456 addresses or 340 undecillion addresses.
    * Top*level domains:
        * The last word in a domain name is the top level domain
        * The second word in a domain name is knowns as a second level domain name (this is optional and it depends on the domain name)
        * Controlled by Internet Assigned Numbers Authority in a root zone database which is essentially a database of all available top level domains.
            * You can view this database at http://www.iana.org/domains/root/db
    * Domain Registrars:
        * Because all of the names in a given domain name have to be unique there needs to be a way to organize this all so that domain names aren't duplicated. This is where domain registrars come in. A registrar is an authority that can assign domain names directly under one or more top level domains. These domains are registered with InterNIC, a service of ICANN, which enforces uniqueness of domain names across the internet. Each domain name becomes registered in a central database known as the WhoIS database.
    * Start of Authority Record (SOA Record)
        * SOA Records hold:
            * The name of the server that supplied the data for the zone
            * The administrator fo the zone
            * The current version of the data file
            * The default number of seconds for the time*to*live file on resource records
    * NS Record * Name Server Records
        * They are used by Top Level Domain servers to direct traffic to the Content DNS server which contains the authoritative DNS records.
    * A Record
        * Most fundamental type of DNS record
        * Address record
        * Used by a computer to translate the name of the domain to an IP Address
    * What is a TTL?
        * The length that a DNS record is cached on either the resolving server or the users own local PC is equal to the value of the TTL in seconds. The lower the TTL, the faster changes to DNS records propagate throughout the internet.
        * Most providers default TTL is 48 hours
    * CNAME Record
        * Canonical Name record can be used to resolve one domain to another.
    * Alias Records
        * Used to map resource record sets in your hosted zone to Elastic Load Balancers, CloudFront Distros, or S3 buckets that are configured as websites.
        * Alias records work like a CNAME record in that you can map one DNS name to another 'target' DNS name.
            * A CANME can't be used for naked domain names (zone apex records). You can't have a CNAME for http://acloud.guru, it must be either an A record or an Alias
    * Exam Tips:
        * ELBs do not have a pre*defined IPv4 address; you resolve them using a DNS name.
        * Understand the difference between an Alias Record and a CNAME
        * Given the choice, between an Alias Record and a CNAME, choose Alias
        * Common DNS Record Types:
            * SOA Records
            * NS Records
            * A Records
            * CNAMES
            * MX Records
            * PTR Records

# Simple Routing Policy
* In a simple routing policy, you can have only one record with multiple IP addresses.
  * If you specify multiple values in a record, Rotue 53 return all values to the user in a random order

# Weighted Routing Policy
* Allows you to split traffic based on different weights assigned.

# Latency Based Routing Policy
* Allows you to route your traffic based on the lowest network altencyt for your end user.
* To use latency based routing, you create a latency resource record set for the EC2 or ELB resource in each reachion that hosts your website. When Route 53 receives a query for your site, it selects the latency resource record set for the region that gives the user the lowest latency. Route 53 then response with teh value associated with that record set.

# Failover Routing Policy
* Failover routing is used to create an active/passive set up.
  * So if you want your primary site to be in eu-west-2 and your secondary DR site in ap-southeast-2.
* Route 53 will monitor the health of your primary site using a health check, if the health check fails, it will switch the the secondary.

# Geolocation Routing Policy
* Lets your choose where your traffic will be sent based on the geographic location of your users.
  * You may want all queries from Europe to be routed to a fleet of EC2 instances that specifically configured for your european customers. These servers may have the local language of your european customers and all prices displayed in euros.
  * If you really want to lock down what instances users in specific regions are using, this is the policy you want.

# Geoproximity Routing
* Geoproximity routing lets Amazon Route 53 route traffic to your resources based on the geographic location of your user AND your resources.
* You can also optionally choose to route more traffic or less to a given resource by specifying a value, known as a bias.
    * A bias expands or shrinks the size of the geographic regions from which traffic routed to a resource.
* To use Geoproximity Routing, you must use Route 53 traffic flow.

# Multivalue Answer Routing Policy
* Lets you configure Route 53 to return multiple values in response to DNS queries.
* You can specify multiple values for almost any record, but multivalue answer routing also lets you check the health of each resource, so Route 53 only returns values for healthy resources.
    * Route 53 responds to DNS queries with up to eight healthy records and gives different answers to different DNS resolvers. The choice of which to use is left to the requesting service effectively creating a form or randomization.
* Think of this as a simple routing policy with a health check on each record set.

# Summary
* ELBs do not have a pre*defined IPv4 address; you resolve them using a DNS name.
* Understand the difference between an Alias Record and a CNAME
* Common DNS Record Types:
    * SOA Records
    * NS Records
    * A Records
    * CNAMES
    * MX Records
    * PTR Records

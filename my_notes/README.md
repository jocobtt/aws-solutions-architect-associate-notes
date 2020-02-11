# Notes for AWS Certified Solutions Archtect Asso. 

The concept for this part of the notes is to have a spot for me to brain dump the things that I have learned in a hopefully easy to understand format for me to then return back to and review for the test. I am also hoping that me writing my notes up in a way that I find easier to understand may lead to me better internalizing the concepts for the test. 

I obviously have also followed the recommendations of this original repo going through the [ACloudGuru Course] (https://www.udemy.com/course/aws-certified-solutions-architect-associate/?LSNPUBID=aosskmXRdYk&ranEAID=aosskmXRdYk&ranMID=39197&ranSiteID=aosskmXRdYk-vfqUztB4B3ydL0VaY7FILg). 

I have also been currently going through the [Jon Bonso Udemy practice tests] (https://www.udemy.com/course/aws-certified-solutions-architect-associate-amazon-practice-exams/?LSNPUBID=aosskmXRdYk&ranEAID=aosskmXRdYk&ranMID=39197&ranSiteID=aosskmXRdYk-gfSGHMiYripNMxK9l95srQ) in my preparation.   



### Contents 
- [IAM](#IAM)
- [S3](#S3)
- [CloudFront](#CloudFront)
- [Storage Gateway](#Storage-Gateway)
- [EC2](#EC2)
- [CloudWatch](#CloudWatch)
- [EFS](#EFS)
- [RDS](#RDS)
- [DynamoDB](#DynamoDB)
- [Redshift](#Redshift)
- [Aurora](#Aurora)
- [Elasticache](#Elasticache)
- [Route53](#Route53)
- [VPC](#VPC)
- [NAT](#NAT)
- [ACL](#ACL)
- [ELB](#ELB)
- [CloudFormation](#CloudFormation)
- [SQS](#SQS)
- [SWF](#SWF)
- [SNS](#SNS)
- [Elastic Transcoder](#Elastic-Transcoder)
- [API Gateway](#API-Gateway)
- [Kinesis](#Kinesis)
- [Web Identity Federation - Cognito](#WIF-Cognito)
- [Lambda](#Lambda)
- [Test Tips](#Test-Tips)
- [Test Layout](#Test-Layout)


# IAM
#### Users

#### Roles

#### Permissions

#### MFA


# S3

### KMS


# CloudFront

# Storage Gateway

# EC2
### Bastion Hosts
# CloudWatch

# EFS

# RDS

What is RDS::


Read replicas can be multi-AZ must have backups turned on for original DB. 
### Overall 
- Can force a failover from one RDS AZ to another by doing a reboot - can click reboot w/ failover 
- can't log into RDS instances (to patch instances etc) at all. Patching etc. is AWS's responsibility 
- Performance needs read replicas
- multi AZ is for disaster recovery (only on SQL, Oracle, MySQL, PostGres & mariaDB)
- two types of backups: auto back up(scheduled maintenence window) and database snapshot (manually performed)
- A read replica can be promoted to master but this will break the read replica.
- encryption at rest is available on MySQL, Oracle, SQL server, PostGRE, MariaDB, and Aurora - done through KMS.

# DynamoDB

AWS's NOSQL database offering. *what it is*

Stored on SSD storage, spread across 3 geo distinct data centers. Has a default of eventual consistent reads and strongly consistent reads. Eventual consistency w/in one sec read from write strongly consistent - w/in less than one sec read. 
--should have more about this**

# Redshift
*what it is*

Can be either single node or multi-node. Redshift is OLAP, BI/Data warehousing.

Multi-node: leader node makes client connections and recieves queries compute node (store data & perform queries/computations) up to 128 compute nodes 
compresses columns. 
Redshift backups - enabled by default w/ 1 day retention period, max 35 days at least 3 copies of your data (replica on compute nodes, original and backup on S3).

### Overall
- Redshift can asynchronously replicate your snapshots to S3 in another region for Disaster recovery.
- redshift priced on compute node hours - 1 unite - per node per hour not charged for leader node hours 
- charged for backups & data transfer (only w/in VPC).
- Always encrypted using SSL transit.
- Encrypted @ rest using FES - 256 by default redshift handles key mgmt. 
- new RDS DB instances automated backups are enabled by default 
- OLTP is best/most suited w/ RDS
- no charge incurred when replicating data from primary RDS to secondary RDS instance. 
- RDS DB security groups - instance port # auto applied to RDS DB security group

Availability - currently available in one AZ (non multinode AZ) can restore snapshots to new AZ in DR




# Aurora 

# Elasticache

# Route53
Consisting of 4-5 Q's on exam

### DNS 101
- DNS is like a phone book linking web names to ip addresses
- CNAME= canonical name- used to reolve one domain name to another ie route mobile traffic address and desktop address to same address - reference. 
- Elastic load balancers don't have pre-defined IPv4 addresses. You resolve to them using a DNS name
- Understand diff between CNAME & alias record - CNAME = references traffic to diff address. 
- Alias records = map resource record sets in your hosted zone to ELB, S3 or cloudfront distributions
- given the choice always choose an alias record over CNAME **

Routing Types:
-------------

Simple Routing - one record w/ multiple IP addresses. If specify multiple values in a record route53 returns all values to user in random order. 

Weighted Routing Policy - allows you to split your traffic to regions based on diff weights assigned ie 10% of traffic to x & 90% to y. 
- health checks set on indv recordsets, if fails check it is removed until it passes.

Latency Routing Policy - Route traffic based on lowest network latency for your end user. Create latency based routing by creating latency resources record set for resource (EC2 or ELB) that hosts website. 

Failover Routing Policy - Used to create active/passive set up - ie primary & scondary regions & monitor with health checks if primary (active) fails sends traffic to secondary (passive)

Geolocation routing - choose where to send traffic based on location of user

geo proximity routing - route traffic to resources based on geographic location of your users & resources. 

multi value answer policy - same as simple routing but can put health checks on each record set. 

# VPC
5-10 Q's on this - way important. Should be able to build a VPC from memory!!
VPC = virtual private cloud - virtual data center in the cloud ie logically isolated section of AWS where you have complete control 


What can we do w/ a VPC? - launch instances into subnet of our choosing. 
Assign custom IP ranges between each subnet, configure route tables between subnets, create internet gateway & attach to VPC, better security control, security groups & subnet NACLS

### VPC Flow Logs 
*what is this*
- Can't enable flow logs for VPCs that are peered w/ your VPC unless the peer VPC is in your account. 
- You can't turn a flow log 
- after you've created a flow log, you can't choose it's config ie can't associate a diff IAM role w/ the flow log 
- not all IP traffic is monitored 


### VPC endpoints
- privately connect your VPC to AWS services & endpoints don't require public IP addresses to configure?
- 2 types - interface & gateway
- interface endpoint- an elastic network interface w/ a private IP that serves as an entry point for traffic destined to a supported device. 
- gateway endpoint - 

### Overall/Summary
- Network ACL's are stateless(no session info is retained by the receiver updates aren't automatic) & do allow rules & deny rules - 1st line of defense.
- Security groups are stateful(session info is stored updates are automically implemented) & second line of defense for instance.
- Default VPC is user friendly - immedietley deploy instances, all subnets in default VPC have a route out to the internet, each EC2 instance was both a public & private IP address.  
- When you create a VPC by default a route table network ACL & security group are created 
- subnets can't span across AZs - one subnet per one AZ 
- Only one internet gateway per VPC 
- Internet gateways are built to be highly available 
- security groups are per VPC 
- VPC peering - connect one VPC w/ another via direct network route using private IP address. 
...Instances behave as if they were on the same private network. 
...Star configuration - ie 1 central VPC peers w/ 4 others. Can't talk to one VPC through another - have to peer any instance you want to have talk to each other. 
- Network ACL's order trumps rules - rule higher up will cancel out one lower - ie rule 1 over rules contradictory rule 5 etc. 

# NAT Gateway
*what it is*
NAT = network address translation. NAT instance single EC2 instance to translate network address. NAT gateway is highly available gateway. Gateway for multiple instances etc. 
- when creating NAT instance disable source/destination check on instance. 
- NAT instances must be in the public subnet
- must be routed out of pulbic subnet to NAT instance in order for it to work
- amount of traffic NAT instance can support depends on the instance size
- NAT instances always behind a security group. 
- redundant inside AZ - not single ec2 instance - can't have multiple NAT gateways inside one AZ
- start at 5 Gops & scales currently to 45 Gops
- no need to patch OS
- not associated w/ security groups 
- auto assigned a public IP
- remember to update your route tables
- no need to disable source/destination checks 


# NACL
*what it is*
Default is to deny inbound & outbound traffic. 
Order matters - if deny is below allow, allow will trump deny etc. 

# ELB/HA
Elastic Load Balancer. How you allow your applications - ie EC2 instances - to automatically distribute traffic to your application across multiple targets. There are 3 types of ELB's - classic, application, and network load balancers. There will be around 10 or so questions on this. Should most likely read the FAQs for this one. 

ELBs run health checks on an instance by talking to the instance. 

Load balancers have their own DNS name, never given an IP address though. How you quickly deploy and manage Apps in AWS w/out worrying about infrastructure. 

### Classic Load Balancer
Provides basic load balancing across multiple EC2 instances and operates at both request and connection level. Intended for apps that were built w/in the ec2-classic network. Legacy load balancer that can load HTTP/HTTPS applications. Cheapest option. Think of an old car. 

### Application Load Balancer
Best suited for load balancing of HTTP and HTTPS traffic and provides advanced request routing targeted at the delivery of modern apps. App load balancers are intelligent and can create advanced request routing, sending specific requests to specific web servers. Think of a 

### Network Load Balancer
Best suited for TCP traffic where extremem performance is required. Can handle millions of requests per second, while maintaining ultra low latencies. Used for extreme performance - think sports car. 

### Sticky Sessions 
allow you to bind user's sessions to specific instances so that all requests from the user during the session are sent to same instances (ie classic load balancer). *Userful if storing user info locally to instance*

### Cross Zone Load Balancer
Can send traffic accross AZ between diff instances. 

Path Patterns = allows you to direct traffic to diff EC2 instances based on the URL contained in the request. 


### Overall Stuff 
- Should always plan for failure. 
- To keep HA always use multi AZ's & regions wherever you can.
- *Know diff between multi AZ's & read reps for RDS* 
- always consider cost element for Q's on AWS exam 
- know diff between S3 storage classes only S3 single AZ & S3 RRS are lower avail
- 

# CloudFormation
How you build quickstart. It's a way of completely scriptuing your cloud environments. 

Quickstart= bunch of cloudformation templates already built by AWS solutions architects. To build complet environments w/ click of button 

# Elastic Bean Stalk
Will probably have 1-2 Q's on test. 
For Developers who don't know AWS - just want to push their app to the cloud, just work on code. 

# SQS
Fully managed queuing service that enables you to decouple and scale microservices, distributed systems, and serverless apps. Can be used to store messages while waiting for a computer to process them. Pull based not push based. Messages can be up to 256 KB in size. Can be stored 1 min - 14 days. Default retention is 4 days. Time out max is 12 hours. SQS guarantees message will be processed at least once. Long polling - doesn't return response until message arrives in message queue or long poll times out. 

Visibility timeout - time a message is invisible in SQS queue after a reader picks up message if not processed w/in time another reader grabs it.  

Two types of message queues: standard and FIFO. 

Standard offers max throughput, best-effort ordering, and at-least-once delivery. The default queue type. Nearly unlimited number of transactions per sec - guaranteed msg delivered at least once. Generally delivered in same order they are sent in. Give multiple messages at times too. 

FIFO queues are designed to guarantee that messages are processed exactly once, in exact order that they are sent. Orders what you get and only gives you one message. 

# SWF
Simple workflow is how you coordinate work across distributed app components. Can have/include a human element in the workflow ie manual process.

SWF actors: workflow starters, deciders and activity workers. 

## SWF vs. SQS 
- SQS has retention period of up to 14 days. SWF workflow executions can last up to 1 year.
- AWS SWF presents a task oriented API. SQS offers message oriented API. 
- SWF ensures a task is assigned only once & is never duplicated. w/ SQS you need to handle duplicated msgs & may also need to ensure that a message is processed only once. 
- SWF keeps track of all tasks & events in an app. With SQS you need to implement own app - level tracking, especially if app uses multiple queues.  

# SNS
Sends notifications from cloud to subscribers/apps. Pushes notifications to devices or SMS messages (text/email etc.). All messages published to SNS are stored redundantly across multi AZ. 

Benefits 
--------
- instantaneous push based delivery (no polling)
- simple API's & easy app integration
- flexible message delivery over multi transport protocols 
- inexpensive pay as you go model w/ no up front costs 
- web based point and click managment interface 

SNS pushes, SQS polls(pulls). SNS and SQS both message. 

# Elastic Transcoder 
- How you transcode media in the cloud. 
- Allows you to convert media files from original format into different formats ie pc etc. 
- Provides transcoding presets for popular output formats ie iphone etc. 
- pay based on minutes you transcode & resolution at which you transcode. 

# API Gateway 
*what it is*
- Publish, maintain, & secure API's (don't need EC2 connections) 
- used usually to be connected to Lambda functions
- exposes HTTPS endpoints to define a restful API
- severless-ly connect to services like lambda & DynamoDB
- send each API endpoint to diff target 
- run efficiently w/ low cost 
- scale effortlessly 
- track and control usage by API key 
- throttle requests to prevent attacks
- connect to cloudwatch to log all requests for monitoring
- maintain multiple versions of your API

Deploy API to stage. Uses API gateway domain by default, can use custom domain, now supports AWS cert manager: free SSL/TLS certs

### Cors(cross-origin resource sharing) in action:
- browser makes HTTP options call for URL 
- server returns a response "these other domains are approved to get this URL"
- Error - "Orgin policy cannot be read at the remote resource" = need to enable CORS on API gateway 

### Overall 
- API gateway is low cost and calse automatically 
- Has caching capabilities to increase performance 
- can throttle API gateay to prevent attacks 
- can log results to cloudwatch 
- if using JavaScript/AJAX that uses multiple domains w/ API gateway, ensure you have enabled CORS on API gateway 
- CORS enforced by the client (your browser)

# Kinesis 
Streaming data (continuously generated by thousands of data sources). 
kinesis is platform to send streaming data to. 
3 kinds of kinesis: streams, firehose & analytics. *Know difference between the 3*

### stream
- allows you to persistently store data for 24hr-7 days 
- have devices or producers produce data that gets sent to kinesis stream 
- stored for 24hr-7days (retention)
- data contained in shards 
- consumers (ie EC2 instances) go in and consume data in shards
- once data is used analytics can then be stored in database service ie S3 RDS etc. 

### Firehose 
```mermaid
graph TD;
    Producers-->Firehose;
    Firehose-->S3 etc.(ElasticSearch);
    S3 etc.-->Redshift;
```

No data persistence for firehose - need to do something with the data as it comes in - optional to have lambda functions.

### Analytics 
Works with firehose & streams and can analyze data on the fly in either service then stores data in either S3, redshift etc. 


# WIF - Cognito 

# Lambda 
Lambda is compute service where you can upload your code and create a function, lambda takes care of provisioning and managing the servers and resources. No need to worry about the OS, patching etc. 

Can use as: 
- an event driven compute service where lambda runs your code in response to events 
- as a compute service to run your code in response to HTTP requests using API gateway or API calls made using AWS SDKs

Only need to worry about your code. Lambda= ultimate abstraction layer.
- data ceners, hardware, assemply, high level languages, operating systems, app layer/AWS API's, AWS lambda. 
- need a DB that's serverless if using a DB w/ lambda 
- Priced by # of requests or duration of requests
- lambda scales out (not up) automatically 
- lambda functions are independent, 1 event = 1 function
- lambda is serverless
- know what services are serverless (for test) ie lambda etc. **
- Lambda functions can trigger other lambda functions, 1 event = x functions if functions trigger other functions
- architectures can get extrememly complicated, use AWS x-ray to debug
- lambda can do things globally, ie backup s3 buckets to other s3 buckets 
- know lambda triggers**
- Know what can't trigger AWS

### Diff types of architecture 
Traditional architecture:
```mermaid
graph TB
    Load Balancer-->EC2;
    EC2-->RDS(can't scale super well w/out issues);
```

Serverless architecture:
```mermaid
graph TL
    API Gateway-->Lambda;
    Lambda-->DynamoDB;
```


# Test Tips

# Test Layout


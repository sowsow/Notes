# Final notes

## Security

- **AWS Organizations Service Control Policy (SCP)** defines the maximum permissions for account members of an organization or organizational unit (OU)
- **SCPs** limit permissions that identity-based policies or resource-based policies grant to entities (users or roles) within the account, but **do not grant permissions**.
- **Systems Manager Parameter Store**
  - does not natively perform rotation of credentials so this must be done in the application
  - SSM parameter is still not supported for Elastic Beanstalk
- **AWS Secrets Manager** does perform credential rotation

## IAM

- You need to **activate IAM user access** to the Billing and Cost Management console and **attaching an IAM policy** to your users. Then, you need to **activate IAM user access for IAM policies** to take effect
- **CloudFront Key Pairs** - IAM users can't create CloudFront key pairs. You must log in **using root credentials** to create CloudFront key pairs
- **IAM username and password** credentials **cannot** be used to **access CodeCommit**
- **Key Pairs** are used for **SSH access** to Amazon EC2 instances. You **cannot** use them in API calls to AWS services
- **IAM roles and policies** can be used for controlling who can **invoke, create** and **manage** your **APIs (API Gateway)**
- **IAM roles and resource-based policies** delegate access across accounts only within a **single partition**

## CodeCommit

- **CodeCommit** with **3** types of credentials:
  - **Git credentials**, an IAM-generated username and password pair communicate with repo over **HTTPS**
  - **SSH keys**, a locally generated public-private key pair communicate with repo over **SSH**
  - **AWS access keys**, be used with the credential helper included with the **AWS CLI** to communicate with repo over **HTTPS**.

## CodeBuild

- **CodeBuild** all environment variables (all names + all values ) maximum of **5,500** characters
- **EC2 Systems Manager Parameter Store** can store an **individual** environment variable (name + value) of maximum **4KB** characters

## CodeDeploy

- Two deployment type options – **in-place** and **blue/green**
- **Lambda** and **ECS** deployments **cannot use an in-place** deployment type
- **Lambda hooks** - **appspec.yaml** - BeforeAllowTraffic > AfterAllowTraffic
- **ECS hooks** - **appspec.yaml** - BeforeInstall > AfterInstall > AfterAllowTestTraffic > BeforeAllowTraffic > AfterAllowTraffic
- **EC2 hooks** - **appspec.yml**- BeforeInstall > AfterInstall > ApplicationStart > ValidateService
- **Lambda** or **ECS** deployment must be **appspec.yaml**
- **EC2** or **On-Premises** deployment must be **appspec.yml**
- **CodeDeploy Agent** archives revisions and log files on instances, and cleans up these artifacts to conserve disk space

## CodePipeline

- CodePipeline **cannot** be configured as a trigger for Lambda

## Elastic Beanstalk

- Beanstalk uses **EC2 instances** for its deployment, hence cannot be called a serverless architecture.

## CloudWatch

- **Standard monitoring - 5mins**
- **Detailed monitoring - 1min** is enabled by default when you create a launch configuration using the AWS CLI or an SDK
- Custom Metric (RAM)
  - Standard: 1 minute (60 seconds)
  - High Resolution
    - Store - 1s
    - Retrieve - 1s, 5, 10, 30, 60X
    - CloudWatch Alarms - 10s or 30s
- Typically **Lambda** functions are triggered as a response to a CloudWatch Event
- CloudWatch Events **cannot** identify and notify failures in the Lambda code

## ELB

- To use **sticky sessions**, the clients must support **cookies**

## EBS

- **Encryption by default** is a **Region-specific setting**. If you enable it for a Region, you cannot disable it for individual volumes or snapshots in that Region.

## EC2

- **Regional Reserved Instance**
  - does not provide a capacity reservation
  - discount applies to instance usage within the instance family, regardless of size. Only supported on Amazon Linux/Unix Reserved Instances with default tenancy
- **Zonal Reserved Instances**
  - provides a capacity reservation in the specified Availability Zone
  - no instance size flexibility. The Reserved Instance discount applies to instance usage for the specified instance type and size only
- **Spot Instance - interruption behavior**
  - Stop the Spot Instance
  - Hibernate the Spot Instance
  - Terminate the Spot Instance **(default)**
- aws ec2 monitor-instances --instance-ids i-1234567890abcdef0
- aws ec2 run-instances --image-id ami-09092360 --monitoring Enabled=true

## API Gateway

- **API Cache of stage - TTL 300s (default) -- 3600s**. TTL=0 => caching is disabled
- does not support **STS**

## ECS

- You can specify **task placement strategies (Binpack/Random/Spread)** with the following actions: **CreateService**, **UpdateService**, and **RunTask**
- **Cluster Query Language** are expressions that enable you to **group container instances** by attributes such as Availability Zone, instance type, or custom metadata

## SQS

- **ReceiveMessage API** call retrieves **up to 10** messages
- **Inflight Messages** - **120,000** (received from a queue by a consumer, but not yet deleted from the queue).
- **Message Retention** - **4 days (default) - 14 days**
- **Message Size** - **256KB**
- **Message Visibility Timeout** - **0s - 30 seconds (default) - 12hrs**
- **Message Delay/Timer** - **0s (default) - 15mins**
- **Message Long Polling** - **1s - 20s**
- **Amazon SQS Extended Client Library** for Java is useful for storing and consuming messages up to **2 GB**

## SNS

- Up to **10m** subscriptions per topic
- **100k** topics limit

## Kinesis

- **Data Retention** - **1 day (default) - 7 days**
- **Write PER SHARD** - 1MB/s / 1000 messages/s
- **Read PER SHARD** - 2MB/s

## Lambda

- **DynamoDB Stream**, **Kinesis Data Stream**, and **Amazon SQS** -- **Synchronously** -- **Event Source Mapping** -- configuration is **made on the Lambda side**
- **Amazon S3** and **SNS** -- **Asynchronously** -- configuration is **made on the source (S3/SNS) side**
- **Memory** in **64MB** increments - **128MB** - 1792 ( = 1 vCPU) - **3008MB**
- **All** environment variables shouldn't exceed **4KB**.

## Misc

- **Access Advisor** feature on IAM console - To help identify the unused roles
- **IAM Access Analyzer** - AWS IAM Access Analyzer helps you identify the resources in your organization and accounts, such as Amazon S3 buckets or IAM roles, that are shared with an external entity
- **Amazon Inspector** is an automated **security assessment service** that helps **improve** the security and compliance of applications deployed on AWS
- **AWS Trusted Advisor** is an online tool that provides you **real time guidance** to help you **provision resources** following AWS best practices
- **AWS Glue** is a fully managed extract, transform, and load (ETL) service that makes it easy for customers to prepare and load their data for analytics.



# AWS Certification Notes

## AWS Regions

- Each region has AZ
- Each AZ is a physical data center in the region
- AWS Consoles are region scoped, except for **IAM and S3**

## IAM

- IAM is a global service, encompasses all regions
- Identity and Access Management
- Roles
  - Internal usage within AWS resources
  - For machines
- Policies apply on User, Groups and Roles
- One IAM **User** per Physical Person
- One IAM **Role** per Application
- Never use Root IAM credentials

## SSH

```chmod 0400 EC2Tutorial.pem```

```ssh -i EC2Tutorial.pem ec2-user@13.239.119.184```

## Security Groups (EC2 firewall)

- Access to Ports
- Authorized IP ranges - IPv4 and IPv6
- Control of inbound/outbound network
- Can be attached to multiple instances
- Locked down to a region/VPC combination
- Live outside the EC2
- Good to maintain one separate security group for SSH access
- All inbound traffic is blocked by default
- All outbound traffic is authorized by default
- "App time out" -> security group issue
- "connection refused" -> an app error or app is not launched
- Security Group (\*)<--->(\*) EC2 Instance
- Security Group can reference IP address, CIDR block and other security groups, except for DNS name

## Elastic IP

- EC2 instance changes its public IP after restart
- If you need a fixed public IP for your instance, you need an Elastic IP
- You can only have 5 Elastic IP in your account, ask AWS to increase
- Try to avoid using Elastic IP, use a random public IP and register a DNS name to it
- Use Load Balancer and do not use a public IP

## EC2 Instance Launch Types

| Instance Types                 | Description                            |
| -----------------------------  | -------------------------------------- |
| On Demand Instances            | short workload, predictable pricing    |
| Reserved Instances             | long workloads (>= 1 year)             |
| Convertible Reserved Instances | long workloads with flexible instances |
| Scheduled Reserved Instances   | launch within time window you reserve  |
| Spot Instances                 | short workloads, for cheap, can lose instances |
| Dedicated Instances            | no other customers will share your hardware |
| Dedicated Hosts                | book an entire physical server, control instance placement (>= 3 years) |

## Burstable Instances (T2)

- AWS has the concept of burstable instances (T2 machines)
- Burst means that overall, the instance has OK CPU performance
- When the machine needs to process something unexpected, it can burst, and CPU can be VERY good
- If the machine bursts it utilizes "burst credits"
- If all the credits are gone, the CPU becomes BAD
- If the machines stop bursting, credits are accumulated over time

## AMI (Amazon Machine Images)

- Custom AMI are built for a specific AWS region, not globally available
- Images can be customized at runtime using EC2 User Data
- AMI is an image to use to create our instances
- AMIs can be built for Linux or Windows machines

## ENI (Elastic Network Interface)

- Logical component in a VPC that represents a virtual network card
- The ENI can have following attributes
  - Primary private IPv4, one or more secondary IPv4
  - One public IPv4
  - One Elastic IP (Ipv4) per private IPv4
  - One or more security groups
  - A MAC address
- You can create ENI independently and attach them on the fly or move them on EC2 instances for failover
- Bound to a specific AZ

## Hight Availability & Scalability for EC2

- Vertical Scaling: Increase instance size (= scale up / down)
  - t2.nano -> u-l2tbl.metal
- Horizontal Scaling: Increase number of instances (= scale out / in)
  - Auto Scaling Group
  - Load Balancer
- Hight Availability: Run instances for the same application across multi AZs
  - Auto Scaling Group multi AZ
  - Load Balancer multi AZ

## Load Balancing

- Load balancers are servers that forward internet traffic to multiple servers (EC2 Instances) downstream
- Spread load across multiple downstream instances
- Expose a single point of access (DNS) to application
- Seamlessly handle failures of downstream instances
- Do regular health checks on instances
- Provide SSL termination (HTTPS) to your websites
- Enforce stickiness with cookies
- High availability across zones
- Separate public traffic from private traffic

## Why use EC2 Load Balancer

- An ELB (EC2 Load Balancer) is a managed load balancer
- Cheaper to setup your own LB, but it will be more effort
- Integrated with many AWS offerings and services

## Load Balancer Security Groups

- The Security Group on EC2 instance can reference the Security Group of Load Balancer which has HTTP/HTTPS security groups with specific ports that allows client to send requests
- LBs can scale but not instantaneously - contact AWS for a "warm-up"
- Troubleshooting
  - 4xx errors are client induced errors
  - 5xx errors are application induced errors
  - Load Balancer Errors 503 means at capacity or no registered target
  - If the LB cannot connect to application, check your security groups
- Monitoring
  - ELB access logs will log all access requests
  - CloudWatch Metrics will give you aggregate statistics, e.g. connection counts

## Classic Load Balancer (V1 - old generation) - 2009

- HTTP (Layer 7), HTTPS (Layer 7), TCP (Layer 4)
- Health checks are TCP or HTTP based
- Fixed hostname, XXX.region.elb.amazonaws.com

## Application Load Balancer (V2 - new generation) - 2016

- Layer 7 only, forward HTTP, HTTPS, WebSocket
- Load balancing to multiple HTTP applications across machines (target groups)
- Load balancing to multiple applications on the same machines, e.g. containers
- Support for HTTP/2 and WebSocket
- Support redirects (from HTTP to HTTPS)
- Listeners -> Rules -> target group(s)
- Routing tables to different target groups:
  - Routing based on path in URL (/user -> /posts)
  - Routing based on hostname in URL (a.b.com -> c.b.com)
  - Routing based on Query String and Headers (b.com/users?id=123&order=false))
- ALB are great fit for micro-services & container-based applications (Docker & ECS)
- Has a port mapping feature to redirect to a dynamic port in ECS
- In comparison, we would need multiple CLB per application
- Target Groups
  - EC2 instances (can be managed by an Auto Scaling Group) - HTTP
  - ECS tasks (managed by ECS itself) - HTTP
  - Lambda functions - HTTP request is translated into a JSON event
  - IP Addresses - must be private IPs
  - ALB can route to multiple target groups
  - Health checks are at the target group level
- Fixed hostname, XXX.region.elb.amazonaws.com
- The application servers don’t see the IP of the client directly
  - The true IP of the client is inserted in the header **X-Forwarded-For**
  - We can also get Port (**X-Forwarded-Port**) and protocol (**X-Forwarded-Proto**)

## Network Load Balancer (V2 - new generation) - 2017

- Layer 4 only, forward TCP, TLS (secure TCP) & UDP protocol
- Handle millions of requests per seconds
- Less latency ~ 100ms (vs 400ms for ALB)
- NLB has **one static IP per AZ** and supports assigning Elastic IP (helpful for whitelisting specific IP)
- New / V2 generation is recommended to use
- You can setup **internal** (private) or **external** (public) ELBs

## Load Balancer Stickiness

- Possible to implement stickiness so that the same client is always redirected to the same instance behind a load balancer
- This works for **CLB** and **ALB**
- ALB -> configured on Target Group level
- The "cookie" used for stickiness has an expiration date you control
- Use case: make sure the user does not lose his session data
- Enabling stickiness may bring imbalance to the load over the backend EC2 instances

## Cross-Zone Loading Balancing

- Each load balancer instance distributes evenly across all registered instances in all AZs
- Otherwise, each load balancer node distributes requests evenly across the registered instances in its AZ only
- CLB
  - Disabled by default
  - No charges for inter AZ data if enabled
- ALB
  - Always on (cannot be disabled)
  - No charges for inter AZ data
- NLB
  - Disabled by default
  - Pay charges for inter AZ data if enabled

## SSL/TLS Certificates

- An SSL Certificate allows traffic between clients and load balancer to be encrypted in transit (in-flight encryption)
- SSL refers to **Secure Socket Layer**, used to encrypt connections
- TLS refers to **Transport Layer Security**, which is a new version
- TLS certificates are mainly used nowadays
- Public SSL certificates are issue by Certificate Authorities (CA)
- SSL certificates has expiration date



















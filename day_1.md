# AWS Cloud & DevOps Interview Preparation

## Day 1 — AWS Fundamentals, Architecture & Troubleshooting

> **Goal:** Build strong AWS fundamentals and develop the engineering thinking required to answer real-world AWS Cloud & DevOps interview questions.

---

## 📌 Day 1 Topics

* Cloud Computing
* AWS Region
* Availability Zone
* High Availability
* Scalability
* Elasticity
* Basic AWS Architecture
* EC2
* Application Load Balancer
* Auto Scaling
* RDS Multi-AZ
* CloudWatch
* AWS Networking
* Security Groups
* Network ACL
* Public IP
* Route Tables
* Internet Gateway
* Application Troubleshooting
* Production Troubleshooting
* Bottleneck Identification

---

# Part 1 — Core Interview Questions

## Q1. What is Cloud Computing, and why do companies use cloud instead of maintaining their own physical infrastructure?

### Key Answer

Cloud computing is the on-demand delivery of IT resources such as compute, storage, networking, and databases over the internet, typically using a pay-as-you-go pricing model.

Companies use cloud computing because they don't need to invest heavily in physical infrastructure upfront. They can provision resources quickly, scale based on demand, improve availability, and pay for the resources they actually use.

### Important Keywords

* On-demand
* Pay-as-you-go
* Scalability
* Elasticity
* High Availability
* Faster provisioning
* No large upfront hardware investment
* Global infrastructure
* Managed services
* Operational efficiency

### Interview Tip

Avoid making "access infrastructure from anywhere" your primary reason.

Focus more on:

> Cost + speed + scalability + elasticity + availability + operational efficiency

---

# Q2. What is an AWS Region and what is an Availability Zone? What is the difference?

## AWS Region

An AWS Region is a geographic area that contains multiple isolated Availability Zones.

## Availability Zone

An Availability Zone (AZ) consists of one or more discrete data centers with independent power, networking, and connectivity within an AWS Region.

### Simple Mental Model

```text
AWS
│
└── Region
     │
     ├── Availability Zone A
     │
     ├── Availability Zone B
     │
     └── Availability Zone C
```

### Main Difference

| Region                              | Availability Zone                                |
| ----------------------------------- | ------------------------------------------------ |
| Geographic area                     | Isolated infrastructure location within a Region |
| Contains multiple AZs               | Exists inside a Region                           |
| Helps with geographic isolation     | Helps with fault isolation                       |
| Region selection can affect latency | Multi-AZ deployment improves availability        |

### Easy Memory Trick

> **Region = Geographic location**

> **AZ = Fault-isolated location inside a Region**

### Why use multiple AZs?

To protect the application against an Availability Zone failure and improve high availability.

---

# Q3. Suppose a company wants to host a web application on AWS. At a high level, what AWS services would you choose and why?

A basic highly available architecture could look like:

```text
                 Users
                   │
                   ▼
                Route 53
                   │
                   ▼
            Application Load
                Balancer
              /           \
             /             \
         AZ-1               AZ-2
          │                   │
        EC2                 EC2
          \                   /
           \                 /
            Auto Scaling Group
                    │
                    ▼
              RDS Multi-AZ
```

Additional services may be introduced depending on requirements:

```text
CloudFront
WAF
S3
CloudWatch
IAM
Secrets Manager
```

### Why Route 53?

DNS management and routing users toward the application endpoint.

### Why Application Load Balancer?

To distribute incoming HTTP/HTTPS traffic across healthy application targets.

### Why multiple AZs?

To improve availability if one AZ becomes unavailable.

### Why Auto Scaling?

To automatically adjust compute capacity according to workload and scaling policies.

### Why RDS Multi-AZ?

To improve database availability through a standby deployment in another Availability Zone.

---

# Q4. You have deployed an application on AWS, but suddenly users report that the application is very slow. How would you troubleshoot it?

A weak answer would be:

> "I will check CPU and memory in CloudWatch."

A stronger DevOps answer uses a systematic investigation.

## Troubleshooting Framework

```text
Understand Impact
       ↓
Check Monitoring
       ↓
Infrastructure
       ↓
Load Balancer
       ↓
Network
       ↓
Application
       ↓
Database
       ↓
External Dependencies
       ↓
Identify Root Cause
       ↓
Mitigate
       ↓
Prevent Recurrence
```

### Step 1 — Understand the Impact

Ask:

* How many users are affected?
* Is the entire application slow?
* Is only one API slow?
* When did the issue start?
* Did anything change recently?

### Step 2 — Check Infrastructure

Use monitoring such as CloudWatch to inspect:

* CPU utilization
* Memory utilization
* Disk utilization
* Disk I/O
* Network traffic
* Instance health

### Step 3 — Check Load Balancer

Check:

* Target health
* Request count
* Target response time
* HTTP 4xx errors
* HTTP 5xx errors

### Step 4 — Check Application

Inspect:

* Application logs
* API latency
* Error rates
* Thread/process utilization
* Slow endpoints

### Step 5 — Check Database

Investigate:

* Database CPU
* Database connections
* Slow queries
* Locks
* Connection exhaustion
* Storage performance

### Step 6 — Check Dependencies

The application may depend on:

* External APIs
* Cache
* Queues
* Other microservices
* Third-party services

### Step 7 — Mitigate

Depending on the root cause:

* Scale infrastructure
* Roll back a bad deployment
* Fix application bottleneck
* Optimize database query
* Increase capacity
* Remove unhealthy targets

### Step 8 — Prevent Recurrence

After resolving the incident:

* Add monitoring
* Improve alerts
* Fix root cause
* Improve scaling policies
* Add capacity planning
* Document the incident

### Important Interview Principle

> **Don't immediately assume that infrastructure is the problem.**

If CPU, memory, and disk are normal but users still experience high latency, investigate the **application, database, network, and dependencies**.

---

# Q5. Your EC2 instance is showing Running, but users cannot access the application through the internet. What could be wrong?

Use a systematic troubleshooting approach.

## Troubleshooting Layers

```text
Internet
   ↓
Internet Gateway
   ↓
Route Table
   ↓
Subnet
   ↓
EC2
   ↓
Security Group
   ↓
Network ACL
   ↓
OS Firewall
   ↓
Application
   ↓
Application Port
```

### Step 1 — EC2

Check:

* Instance state
* EC2 status checks
* Public IP / public DNS
* Correct instance

### Step 2 — Subnet

Check whether the instance is actually in a public subnet.

### Step 3 — Route Table

Verify that the subnet has an appropriate route such as:

```text
0.0.0.0/0 → Internet Gateway
```

### Step 4 — Security Group

For HTTP:

```text
Inbound
TCP 80
Source: 0.0.0.0/0
```

For HTTPS:

```text
Inbound
TCP 443
```

### Step 5 — Network ACL

Check inbound and outbound rules to ensure traffic isn't being blocked.

### Step 6 — OS Firewall

Check:

* UFW
* firewalld
* iptables

### Step 7 — Application

Check whether the application/service is actually running.

Example:

```bash
systemctl status <service>
```

Check listening ports:

```bash
ss -lntp
```

A common problem is that the application is listening only on:

```text
127.0.0.1:8080
```

instead of:

```text
0.0.0.0:8080
```

### Important Correction

Every EC2 instance launched into a VPC.

The important question is whether the instance is in the correct subnet and has the appropriate routing and connectivity for public access.

---

# Part 2 — Cross-Questioning

## Follow-up 1

### Question

The application is experiencing very high latency.

CPU, memory, and disk utilization are normal.

What would you check next?

### Interview Approach

Don't immediately say:

> "I will increase EC2 instances."

Instead investigate other layers.

Check:

1. ALB target response time
2. ALB 4xx/5xx
3. Application logs
4. API latency
5. Database performance
6. Database connections
7. Slow queries
8. Network latency
9. External APIs
10. Cache/dependency issues

### Strong Interview Answer

> If infrastructure metrics are normal but latency remains high, I would move to the application and dependency layers. I would check ALB target response time, application logs, error rates, database performance and connections, slow queries, and external service dependencies. I would use monitoring and tracing where available to identify where the request is spending time.

---

# Follow-up 2

## Question

EC2 has:

* Running status
* Public IP
* Security Group allows HTTP 80
* NACL allows traffic

But the website still doesn't open.

What are your next checks?

### Recommended Order

```text
1. EC2 status checks
2. Confirm public IP/DNS
3. Confirm public subnet
4. Check Route Table
5. Check Internet Gateway
6. Check Security Group
7. Check NACL
8. Check OS firewall
9. Check application/service
10. Check application listening port
```

Useful commands:

```bash
systemctl status <service>
```

```bash
ss -lntp
```

---

# Follow-up 3

## Question

You deployed an application across two Availability Zones behind an Application Load Balancer.

One AZ suddenly goes down.

What happens?

### Answer

The ALB distributes traffic to healthy registered targets.

If targets in one AZ become unavailable, traffic can continue to healthy targets in the remaining available AZ.

Example:

```text
                 ALB
                /   \
               /     \
            AZ-A     AZ-B
             ❌       ✅
                      │
                    EC2
```

Traffic can continue toward the healthy targets.

Auto Scaling may also launch replacement capacity depending on the configured scaling policies and available capacity.

### Important Concept

Don't say:

> "ALB switches the AZ."

Better:

> "The ALB routes traffic to healthy available targets."

---

# Follow-up 4

## Question

What is the difference between High Availability, Scalability, and Elasticity?

---

## High Availability

The ability of a system to remain available despite failure of individual components.

Example:

```text
ALB
├── AZ-A → EC2
└── AZ-B → EC2
```

If one AZ fails, the application can continue operating through the other AZ.

---

## Scalability

The ability of a system to increase or decrease capacity to handle changing workload.

### Vertical Scaling

```text
t3.medium
     ↓
t3.large
```

### Horizontal Scaling

```text
2 EC2
 ↓
5 EC2
```

---

## Elasticity

The ability to automatically adjust resources according to demand.

Example:

```text
Normal traffic
3 EC2
   ↓
Peak traffic
8 EC2
   ↓
Traffic decreases
3 EC2
```

### Easy Memory Trick

> **High Availability = Can I stay available if something fails?**

> **Scalability = Can I grow when workload increases?**

> **Elasticity = Can I automatically adjust capacity according to demand?**

---

# Follow-up 5

## Question

Your application suddenly receives 10× normal traffic.

You say:

> "Auto Scaling will increase EC2 instances."

What else should you consider?

Example architecture:

```text
Users
  ↓
Route 53
  ↓
ALB
  ↓
Auto Scaling
  ↓
EC2
  ↓
RDS
```

### Possible Flow

```text
Traffic increases
       ↓
Load increases
       ↓
Monitoring detects condition
       ↓
Scaling policy triggers
       ↓
New EC2 instances launch
       ↓
Instances become healthy
       ↓
ALB sends traffic to healthy targets
```

### But Auto Scaling Does NOT Automatically Solve Everything

Potential bottlenecks include:

* RDS CPU
* RDS connections
* Slow database queries
* Database locks
* Application thread pool
* ALB configuration
* Network capacity
* External APIs
* Cache
* Message queues
* Third-party rate limits

### Critical Interview Concept

> **Auto Scaling the compute layer does not automatically scale the entire application.**

For example:

```text
                 ALB
                  ↓
        30 EC2 instances
                  ↓
             RDS Database
                  ↓
              Bottleneck
```

Even if EC2 scales from 10 → 30, the database can still become the bottleneck.

---

# Part 3 — Interview Troubleshooting Framework

For production troubleshooting questions, use this framework:

```text
1. Understand the problem
        ↓
2. Determine impact and scope
        ↓
3. Check monitoring/metrics
        ↓
4. Check infrastructure
        ↓
5. Check networking
        ↓
6. Check security
        ↓
7. Check application
        ↓
8. Check database
        ↓
9. Check external dependencies
        ↓
10. Identify root cause
        ↓
11. Mitigate the incident
        ↓
12. Prevent recurrence
```

### Golden Rule

Never jump directly to a solution.

First:

> **Observe → Investigate → Identify → Mitigate → Prevent**

---

# Part 4 — Day 1 Key Takeaways

## AWS Architecture

Remember:

```text
Route 53
    ↓
ALB
    ↓
Multiple AZs
    ↓
EC2 / Application
    ↓
RDS Multi-AZ
```

---

## Core Concepts

```text
Region
    ↓
Multiple Availability Zones
    ↓
High Availability
```

```text
Scalability
    ↓
Ability to increase/decrease capacity
```

```text
Elasticity
    ↓
Automatically adjust capacity according to demand
```

---

# Day 1 Self-Assessment

Before moving to Day 2, make sure you can explain these without looking at notes:

* [ ] What is Cloud Computing?
* [ ] Why do companies use cloud?
* [ ] What is an AWS Region?
* [ ] What is an Availability Zone?
* [ ] Region vs AZ
* [ ] What is High Availability?
* [ ] What is Scalability?
* [ ] What is Elasticity?
* [ ] Scalability vs Elasticity
* [ ] Why use multiple AZs?
* [ ] Why use an ALB?
* [ ] Why use Auto Scaling?
* [ ] Why use RDS Multi-AZ?
* [ ] How do you troubleshoot a slow application?
* [ ] How do you troubleshoot inaccessible EC2?
* [ ] What is a public subnet?
* [ ] What is an Internet Gateway?
* [ ] What is a Route Table?
* [ ] What is a Security Group?
* [ ] What is a NACL?
* [ ] What can become a bottleneck after EC2 Auto Scaling?

---

# 🎯 Interview Mindset

Don't try to memorize every answer word-for-word.

Instead understand:

> **What is it?**

> **Why do we use it?**

> **How does it work?**

> **What happens when it fails?**

> **How do I troubleshoot it?**

> **How do I secure it?**

> **How do I scale it?**

> **How do I reduce its cost?**

If you can answer these questions, you are developing real interview-level engineering thinking.

---

## Day 1 Status

**Foundation:** 🟢 Good

**AWS Architecture:** 🟢 Good

**Networking:** 🟡 Needs practice

**Troubleshooting:** 🟡 Needs significant practice

**Scenario Thinking:** 🟡 Developing

**Interview Confidence:** 🟡 Improving

**Next Topic:** AWS EC2 — Deep Interview Preparation

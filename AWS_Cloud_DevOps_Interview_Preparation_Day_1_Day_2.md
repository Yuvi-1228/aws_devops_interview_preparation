# AWS Cloud & DevOps Interview Preparation
## Day 1 + Day 2 — Questions, Correct Answers & Troubleshooting Notes

> **Goal:** Build strong AWS Cloud/DevOps interview skills from fundamentals to production-level troubleshooting.
>
> **Important:** These answers are interview-ready examples. Understand the logic instead of memorizing word-for-word.

---

# DAY 1 — AWS Fundamentals & Basic Troubleshooting

## Q1. What is Cloud Computing, and why do companies use cloud instead of maintaining their own physical infrastructure?

### Interview-Ready Answer

Cloud Computing is the on-demand delivery of IT resources such as compute, storage, networking, and databases over the internet, usually using a pay-as-you-go pricing model.

Companies use cloud instead of maintaining their own physical infrastructure because cloud provides on-demand resources, scalability, elasticity, faster provisioning, high availability, global access, and reduces the need for large upfront infrastructure investment.

### Key Terms

- On-demand resources
- Pay-as-you-go
- Scalability
- Elasticity
- High availability
- Global infrastructure
- Reduced infrastructure management

---

## Q2. What is an AWS Region and what is an Availability Zone? What is the difference?

### Interview-Ready Answer

An AWS Region is a separate geographic area where AWS provides its cloud infrastructure. An Availability Zone, or AZ, is an isolated location within an AWS Region consisting of one or more data centers.

We choose a Region based on factors such as customer location, latency, compliance, and service availability. We use multiple Availability Zones to improve availability and fault tolerance.

### Simple Example

```text
AWS Region
│
├── Availability Zone A
├── Availability Zone B
└── Availability Zone C
```

### Remember

- **Region = Geographic area**
- **AZ = Isolated infrastructure location inside a Region**
- **Multiple AZs = Higher availability and fault tolerance**

---

## Q3. A company wants to host a web application on AWS. At a high level, what AWS services would you choose and why?

### Interview-Ready Answer

For a highly available web application, I would typically use Route 53 for DNS, an Application Load Balancer to distribute incoming traffic, an Auto Scaling Group with EC2 instances for compute, and RDS for the database.

I would deploy resources across multiple Availability Zones. The Load Balancer would send traffic only to healthy targets, while Auto Scaling would add or remove EC2 instances based on demand. For the database, I would consider Multi-AZ deployment for high availability.

### High-Level Architecture

```text
Users
  ↓
Route 53
  ↓
Application Load Balancer
  ↓
Auto Scaling Group
  ↓
EC2 Instances
  ↓
RDS
```

---

## Q4. The application is very slow. As a Cloud/DevOps Engineer, how would you troubleshoot it?

### Interview-Ready Answer

First, I would understand the impact and scope of the issue. Then I would check monitoring metrics such as CPU, memory, disk I/O, network, and application latency using CloudWatch.

I would determine whether the issue is related to the load balancer, EC2 instances, application, database, or another dependency. I would check service health, logs, recent deployments or configuration changes, and identify the bottleneck before taking corrective action.

### General Troubleshooting Flow

```text
Impact
  ↓
Monitoring
  ↓
Network / Load Balancer
  ↓
EC2 / OS
  ↓
Application
  ↓
Database / Dependencies
  ↓
Logs
  ↓
Root Cause
  ↓
Fix / Mitigation
  ↓
Prevention
```

---

## Q5. EC2 is Running, but users cannot access the application through the internet. What could be wrong?

### Possible Causes

1. Security Group does not allow the required port.
2. Network ACL is blocking traffic.
3. EC2 does not have appropriate public connectivity.
4. Subnet Route Table does not have a route to the Internet Gateway.
5. Internet Gateway is not attached to the VPC.
6. Application/service is not running.
7. Application is not listening on the expected port.
8. Application is listening only on localhost.
9. DNS or Load Balancer configuration is incorrect.

### Interview Approach

I would troubleshoot layer by layer rather than immediately assuming that EC2 being in a Running state means the application is accessible.

---

# DAY 1 — FOLLOW-UP QUESTIONS

## Follow-Up 1. What if users still experience very high latency?

### Interview-Ready Answer

I would continue investigating the complete request path. I would check the Load Balancer metrics and target health, verify whether traffic is distributed correctly, and check Auto Scaling behavior.

I would also investigate application latency, database performance, network latency, and other dependencies to identify the actual bottleneck instead of assuming that adding more EC2 instances will solve the problem.

---

## Follow-Up 2. How would you check whether the application service is running?

### Answer

On a Linux server, I can check the service using:

```bash
systemctl status <service-name>
```

For example:

```bash
systemctl status nginx
```

I can also inspect logs using:

```bash
journalctl -u <service-name>
```

---

## Follow-Up 3. What happens if one Availability Zone goes down?

### Interview-Ready Answer

If the application is designed across multiple Availability Zones, the Application Load Balancer can continue routing traffic to healthy targets in the remaining healthy Availability Zones.

An Auto Scaling Group can also launch replacement capacity according to its configuration and demand. The application must therefore be deployed in a fault-tolerant multi-AZ architecture.

### Important

Do not say:

> "ALB switches the entire AZ automatically."

Better:

> "ALB routes traffic to healthy registered targets, and a multi-AZ architecture allows the application to continue operating when one AZ becomes unavailable."

---

## Follow-Up 4. What is the difference between High Availability, Scalability and Elasticity?

### Correct Answer

**High Availability** means designing a system so that it remains available even when some components fail.

**Scalability** means the ability of a system to handle increased workload by increasing capacity.

**Elasticity** means the ability to dynamically increase or decrease resources according to workload demand.

### Important Correction

Elasticity is **not simply manually increasing resources**.

```text
Scalability:
Can handle more workload.

Elasticity:
Can dynamically adjust capacity according to workload.
```

---

## Follow-Up 5. What if you don't know the answer in an interview?

### Professional Response

> "I haven't encountered that specific scenario yet, but I understand the underlying concept and I would approach it by first identifying the affected layer, collecting evidence from monitoring and logs, and then isolating the root cause. I'm also comfortable learning the specific implementation quickly."

Never falsely claim production experience you don't have.

---

# DAY 2 — EC2, Networking & Production Troubleshooting

## Q1. What is Amazon EC2, and what factors do you consider when choosing an EC2 instance?

### Interview-Ready Answer

Amazon EC2 stands for Elastic Compute Cloud. It provides resizable compute capacity in the AWS cloud, allowing us to run virtual servers for applications and workloads.

When choosing an EC2 instance, I would consider the workload requirements such as CPU, memory, storage, network performance, expected traffic, operating system, scalability, availability, and cost.

### Common Instance Selection Logic

```text
CPU-intensive workload
        ↓
Compute-optimized instance

Memory-intensive workload
        ↓
Memory-optimized instance

General web application
        ↓
General-purpose instance
```

### EC2 Launch vs Instance Selection

Do not confuse these two.

**Launching EC2:**

```text
AMI
 ↓
Instance Type
 ↓
Key Pair / Access Method
 ↓
VPC / Subnet
 ↓
Security Group
 ↓
Storage
 ↓
Launch
```

**Choosing an instance:**

```text
Workload
 ↓
CPU / Memory
 ↓
Network
 ↓
Storage
 ↓
Traffic
 ↓
Performance
 ↓
Cost
 ↓
Scalability
```

---

## Q1 Cross-Question. The application becomes slow during peak traffic. How do you decide between vertical and horizontal scaling?

### Interview-Ready Answer

I would first check CloudWatch metrics and application behavior to identify the bottleneck.

Vertical scaling means increasing the capacity of an existing instance, such as moving from a smaller instance to a larger one.

Horizontal scaling means adding more instances.

If the application is horizontally scalable, I would generally prefer horizontal scaling using an Auto Scaling Group and Load Balancer because it improves scalability and availability. If the workload requires more CPU or memory per individual instance and cannot efficiently scale horizontally, I would consider vertical scaling.

### Remember

```text
Vertical = Bigger machine

Horizontal = More machines
```

Do not decide only from CPU percentage. First understand the workload and whether it can be distributed.

---

# Q2. Explain the complete request path when a user enters https://example.com.

### Interview-Ready Answer

When a user enters the domain, DNS resolution is performed to determine the destination of the application. If Route 53 hosts the DNS record, it returns the configured destination, such as an ALB or an EC2 public address.

The request then travels through the internet toward AWS. If the EC2 instance is in a public subnet, the subnet's route table needs an appropriate route such as:

```text
0.0.0.0/0 → Internet Gateway
```

The Security Group must allow the required inbound traffic, such as TCP 443 for HTTPS. If routing and security rules are correct, the request can reach the EC2 network interface and then the application.

### Production Architecture Often Looks Like

```text
User
 ↓
Route 53
 ↓
ALB
 ↓
Target Group
 ↓
EC2
 ↓
Application
```

### Important Concepts

- Route 53 = DNS
- Internet Gateway = Provides VPC ↔ Internet connectivity
- Route Table = Determines where network traffic is routed
- Security Group = Controls allowed inbound/outbound traffic
- EC2 = Runs the application

### Important Correction

Do not say:

> "0.0.0.0/0 means internet."

Say:

> "`0.0.0.0/0` is a default route for IPv4 destinations and, when pointing to an Internet Gateway, provides a path toward the internet."

---

# Q2 Cross-Question. EC2 has a public IP and Security Group allows port 80, but Route Table has no 0.0.0.0/0 → Internet Gateway. Will the website be accessible?

### Answer

No, the website will not be properly accessible from the internet.

A public IP alone is not sufficient. The subnet needs a route to an Internet Gateway so that internet-bound traffic has a valid network path.

### Concept

```text
EC2
 ↓
Subnet Route Table
 ↓
0.0.0.0/0 → IGW
 ↓
Internet
```

If the route is missing, the required path is not available.

---

# Q3. EC2 and networking look correct, but the website is still unavailable. How do you troubleshoot?

### Interview-Ready Answer

I would first check the exact browser error to determine whether the request is receiving an HTTP response or failing at the connectivity level.

If it is a 500 error, I would investigate the application and web-server logs.

If it is a timeout or connection refused error, I would check whether the service is running and listening on the expected port.

I would use commands such as:

```bash
systemctl status nginx
```

```bash
ss -lntp
```

Then I would test the application locally:

```bash
curl http://localhost
```

Finally, I would inspect application logs and verify the service configuration.

### Error Interpretation

```text
Timeout
  ↓
Investigate connectivity / routing / firewall / listener

Connection refused
  ↓
Check service and listening port

HTTP 400
  ↓
Investigate request/client-side issue

HTTP 500
  ↓
Investigate server/application issue
```

---

# Q3 Cross-Question. Nginx is running, but ss -lntp shows 127.0.0.1:80 instead of 0.0.0.0:80. Why?

### Answer

Nginx is listening only on the loopback interface, `127.0.0.1`. This means it accepts connections only from the local machine.

External traffic arriving through the EC2 network interface cannot reach that listener.

### Concept

```text
127.0.0.1:80
    ↓
Localhost only

0.0.0.0:80
    ↓
Listen on all IPv4 interfaces
```

### Fix

Configure Nginx to listen on the appropriate network interface, for example:

```nginx
listen 0.0.0.0:80;
```

Validate:

```bash
sudo nginx -t
```

Reload:

```bash
sudo systemctl reload nginx
```

Verify:

```bash
ss -lntp | grep :80
```

Test:

```bash
curl http://localhost
```

Then test externally.

### Interview Answer

> "Nginx is running, but it is bound only to the loopback address 127.0.0.1:80. Therefore it accepts local connections but not external connections arriving through the EC2 network interface. I would configure Nginx to listen on the appropriate network interface, validate the configuration with nginx -t, reload it, and verify the listening socket again."

---

# Q4. EC2 application is slow and CloudWatch shows CPU = 95%, Memory and Disk are normal. How do you troubleshoot?

### Interview-Ready Answer

First, I would assess the impact and determine whether the issue affects all users or a specific workload.

Then I would access the EC2 instance and identify the processes consuming CPU using:

```bash
top
```

or:

```bash
ps aux --sort=-%cpu | head
```

After identifying the process, I would investigate why it is consuming high CPU. I would check application logs, recent deployments, traffic spikes, and application behavior.

If the workload is genuinely CPU-intensive, I would consider scaling. For a horizontally scalable application, I would prefer adding instances through an Auto Scaling Group. If the workload cannot be distributed and needs more CPU on a single instance, I would consider vertical scaling.

### Important

Do not automatically do:

```text
CPU high → Vertical scaling
```

Instead:

```text
CPU high
 ↓
Identify process
 ↓
Understand root cause
 ↓
Can workload be distributed?
 ↓
Horizontal or Vertical scaling
```

### Useful Commands

```bash
top
```

```bash
ps aux --sort=-%cpu | head
```

```bash
ps -fp <PID>
```

```bash
journalctl -u <service-name>
```

---

# Q5. ALB returns 502 Bad Gateway. EC2 CPU and memory are normal. How do you troubleshoot?

### Architecture

```text
Users
 ↓
ALB
 ↓
Target Group
 ↓
EC2
 ↓
Application
 ↓
RDS
```

### Interview-Ready Answer

I would first check the ALB metrics and Target Group health to determine whether the issue affects all targets or only specific instances.

Then I would verify the ALB health-check configuration, including protocol, port, path, timeout, and expected response.

If targets are unhealthy, I would connect to an affected EC2 instance and check whether the application service is running and listening on the expected port.

I would test the application locally using curl and check application and web-server logs.

I would also verify the Security Group communication between the ALB and EC2.

If the application is healthy but still returns errors, I would investigate dependencies such as RDS and check for recent deployments or configuration changes.

Finally, I would identify the root cause, mitigate the incident, and implement preventive measures.

### Troubleshooting Flow

```text
502
 ↓
ALB metrics
 ↓
Target Group
 ↓
Target Health
 ↓
Health Check configuration
 ↓
EC2
 ↓
Application service
 ↓
Listening port
 ↓
curl localhost
 ↓
Security Groups
 ↓
Application logs
 ↓
RDS / dependencies
 ↓
Recent deployment/change
 ↓
Root Cause
 ↓
Mitigation
 ↓
Prevention
```

---

# Q6. RDS CPU is 95% and Database Connections are very high. EC2 and ALB are healthy. What is the likely bottleneck?

### Answer

The likely bottleneck is the database layer, specifically RDS.

The application may be healthy at the EC2 layer, but if the database is overloaded, application requests that depend on the database can become slow.

### Investigation

Check RDS CloudWatch metrics:

- CPUUtilization
- DatabaseConnections
- Read/Write IOPS
- Read/Write latency
- FreeStorageSpace
- Throughput

Then investigate:

- Slow queries
- Missing indexes
- Full table scans
- Expensive joins
- Long-running queries
- Connection pool configuration
- Connection leaks
- Database connection limits
- Recent deployments

### Possible Immediate Mitigation

Depending on the root cause:

- Roll back a bad deployment
- Stop or optimize an expensive query
- Correct connection-pool configuration
- Reduce unnecessary database traffic
- Temporarily scale database capacity if appropriate

### Long-Term Solutions

Depending on workload:

- Query optimization
- Proper indexing
- Connection pooling
- Caching
- Read replicas for read-heavy workloads
- Larger RDS instance class
- Better monitoring and alerting

### Important

Do not assume:

```text
Application slow
 ↓
Add more EC2
```

If RDS is already overloaded, adding EC2 instances can create even more database connections and make the problem worse.

---

# UNIVERSAL PRODUCTION TROUBLESHOOTING FRAMEWORK

The exact order is not always identical, but this framework works across many AWS/DevOps incidents:

```text
1. DEFINE
   What exactly is broken?

2. SCOPE
   Who or what is affected?

3. CHANGE
   What changed recently?

4. TRACE
   Where does the request/data path fail?

5. EVIDENCE
   Collect metrics, logs, errors and events.

6. ISOLATE
   Identify the failing layer.

7. MITIGATE
   Restore service or reduce impact.

8. ROOT CAUSE
   Determine why the problem happened.

9. PREVENT
   Monitoring, alerts, automation, testing, documentation.
```

### One-Line Memory Trick

> **DEFINE → SCOPE → CHANGE → TRACE → EVIDENCE → ISOLATE → MITIGATE → ROOT CAUSE → PREVENT**

---

# IMPORTANT AWS TROUBLESHOOTING LAYERS

When an application is not working, think in layers:

```text
User
 ↓
DNS
 ↓
Internet
 ↓
Internet Gateway
 ↓
Route Table
 ↓
Subnet
 ↓
Security Group / NACL
 ↓
Load Balancer
 ↓
Target Group
 ↓
EC2
 ↓
Operating System
 ↓
Port / Listener
 ↓
Application
 ↓
Database
 ↓
External Dependencies
```

The goal is not to guess.

The goal is to find:

> **"At which layer does the request fail?"**

---

# IMPORTANT LINUX COMMANDS FOR INTERVIEWS

## Check service

```bash
systemctl status <service>
```

## Start service

```bash
sudo systemctl start <service>
```

## Restart service

```bash
sudo systemctl restart <service>
```

## Check listening ports

```bash
ss -lntp
```

## Check CPU/processes

```bash
top
```

```bash
ps aux --sort=-%cpu | head
```

## Check process

```bash
ps -fp <PID>
```

## Check service logs

```bash
journalctl -u <service>
```

## Test local HTTP service

```bash
curl http://localhost
```

## Test specific port

```bash
curl http://localhost:8080
```

## Test Nginx configuration

```bash
sudo nginx -t
```

---

# KEY INTERVIEW CONCEPTS TO REMEMBER

## High Availability

System continues operating even when some components fail.

## Scalability

Ability to handle increased workload by increasing capacity.

## Elasticity

Ability to dynamically increase or decrease capacity based on demand.

## Vertical Scaling

```text
2 vCPU / 4 GB
      ↓
4 vCPU / 8 GB
```

Make the existing machine bigger.

## Horizontal Scaling

```text
1 EC2
 ↓
3 EC2
 ↓
10 EC2
```

Add more machines.

## Route 53

AWS DNS service used to route DNS queries to configured destinations.

## Internet Gateway

Provides connectivity between a VPC and the internet.

## Route Table

Contains routing rules that determine where network traffic should go.

## Security Group

Controls allowed inbound and outbound traffic for associated resources/network interfaces.

## NACL

Network-level access control for subnet traffic.

## ALB

Application Load Balancer distributes HTTP/HTTPS traffic to healthy targets.

## Auto Scaling Group

Maintains and adjusts a fleet of EC2 instances according to configured policies and desired capacity.

## RDS

Managed relational database service.

---

# INTERVIEW MINDSET

Do not think:

> "I need to know the answer to every possible production problem."

Think:

> "I need to know how to investigate an unfamiliar problem."

A strong DevOps engineer does not blindly guess. They:

```text
Observe
 ↓
Collect evidence
 ↓
Form a hypothesis
 ↓
Test the hypothesis
 ↓
Isolate the problem
 ↓
Fix / Mitigate
 ↓
Prevent recurrence
```

---

# HONESTY ABOUT PRODUCTION EXPERIENCE

If you have not handled a specific incident in production, do not claim that you have.

Instead say:

> "I haven't encountered this exact issue in production yet, but my troubleshooting approach would be..."

Then explain the technical investigation clearly.

This demonstrates technical understanding without falsely claiming experience.

---

# 60-DAY INTERVIEW PREPARATION TARGET

## Days 1–15
AWS fundamentals + Linux + Networking

## Days 16–30
DevOps tools + Git + CI/CD + Docker + Terraform

## Days 31–45
Production troubleshooting + incidents + logs + monitoring

## Days 46–60
Architecture + scenario questions + mock interviews + project discussion + rapid-fire rounds

### Final Goal

```text
Concept
 ↓
Hands-on
 ↓
Failure Scenario
 ↓
Troubleshooting
 ↓
Cross-question
 ↓
Interview Explanation
```

---

## Revision Rule

For every question, practice explaining:

1. **What is it?**
2. **How does it work?**
3. **Why do we use it?**
4. **What can go wrong?**
5. **How would I troubleshoot it?**
6. **How would I fix it?**
7. **How would I prevent it from happening again?**

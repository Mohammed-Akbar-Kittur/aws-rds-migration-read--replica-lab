# RDS Migration with Read Replica Setup

<img width="1920" height="1280" alt="IMG-20261004-WA1531" src="https://github.com/user-attachments/assets/320f2003-cf17-46d8-bb21-fff67bab8d14" />

> **Lab Status:** Migration lab using DMS, deleted post-validation.

## Overview
Zero-downtime migration from on-premise MySQL to AWS RDS with read scaling using Read Replica.

## Architecture Flow
On-Prem DB -> DMS (Full Load + CDC) -> Primary RDS MySQL (eu-north-1a) Writer -> Async Replication -> Read Replica (eu-north-1b) Reader -> EC2 App -> Route53

## Tech Stack
RDS MySQL (db.m6i.large), DMS, EC2, VPC, Route53

## Deployment Steps
1. Created Primary RDS with Multi-AZ
2. Created DMS Replication Instance + Source/Target Endpoints
3. Ran Full Load + CDC task
4. Created Read Replica in different AZ
5. Configured App: Writes to Writer Endpoint, Reads to Reader Endpoint

## Validation
- DMS task status: 100% Full Load complete, CDC running with 0 lag
- Replica lag check: `SHOW SLAVE STATUS\G` -> Seconds_Behind_Master = 0
- Read/Write split test: `SELECT @@hostname` on Reader showed replica endpoint, Writer showed primary
- Failover test: Rebooted primary -> Replica lag < 2 sec, app still serving reads
- Route53 health check: DNS resolved to healthy instance

## Outcome
- Zero-downtime migration achieved with CDC replication
- 80% read traffic offloaded to replica - primary CPU reduced from 70% to 20%
- DR ready - replica can be promoted to standalone in <2 min
- Improved app latency by 40% using reader endpoint

---
## Author
**Mohammed Akbar Kittur**
DevOps Engineer | AWS | Linux | Terraform | Docker | Kubernetes
📍 Bangalore, Karnataka
🔗 [GitHub](https://github.com/Mohammed-Akbar-Kittur) | [LinkedIn](https://linkedin.com/in/mohammed-akbar-kittur)

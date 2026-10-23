<div align="center">

# Multi-AZ Private Amazon RDS Infrastructure

An enterprise-grade, cost-optimized reference architecture for deploying an isolated Amazon RDS PostgreSQL instance using multi-AZ subnet grouping and security group chaining.

[![AWS](https://img.shields.io/badge/AWS-Cloud-232F3E?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Engine](https://img.shields.io/badge/RDS-PostgreSQL-336791?logo=postgresql&logoColor=white)](https://aws.amazon.com/rds/postgresql/)
[![Security](https://img.shields.io/badge/Security-Principle_of_Least_Privilege-green?logo=auth0&logoColor=white)](#security-architecture)
[![Tier](https://img.shields.io/badge/Cost-AWS_Free_Tier-orange)](https://aws.amazon.com/free/)

</div>

---

## Architecture Overview

This architecture isolates stateful database workloads within dedicated private subnets while enabling administrative access exclusively through a bastion layer in a public subnet.

<div align="center">
  <img src="architecture-diagram.png" alt="AWS Infrastructure Architecture" width="850">
</div>

---

## Technical Specifications

| Component | Identifier / Value | Details |
| :--- | :--- | :--- |
| **Region** | `ap-south-1` (Mumbai) | Dual-AZ deployment |
| **VPC** | `lab-vpc-vpc` (`10.0.0.0/16`) | Custom VPC with DNS hostnames and resolution enabled |
| **Public Subnets** | `10.0.0.0/20` (AZ-a), `10.0.16.0/20` (AZ-b) | Routed to Internet Gateway (`lab-vpc-igw`) |
| **Private Subnets** | `10.0.128.0/20` (AZ-a), `10.0.144.0/20` (AZ-b) | Non-routable; local VPC traffic only |
| **DB Subnet Group** | `rds-private-subnet-group` | Encompasses private subnets across 2 AZs |
| **Database Instance** | Amazon RDS PostgreSQL (`db.t3.micro`) | Single-AZ, 20 GiB gp3, `Publicly Accessible: No` |

---

## Security Architecture

The design implements defense-in-depth through multi-tier isolation and security group chaining:

```text
[ Admin Workstation ]
        │
        │ Inbound SSH (Port 22) [Restricted to Admin Public IP]
        ▼
[ Bastion Host (EC2) ]  ◄── Security Group: app-client-sg
        │
        │ Inbound PostgreSQL (Port 5432)
        ▼
[ Amazon RDS Database ] ◄── Security Group: rds-db-sg (No Public IP)

# Multi-AZ Secure VPC for Financial Systems on AWS

## Project Overview
This project implements a **secure, highly available AWS network architecture** designed for financial reporting systems. The infrastructure follows security best practices for public sector and financial environments, emphasizing **least privilege**, **cost optimization**, and **high availability**.

###  Business Context
- **Industry:** Financial Services / Government Treasury
- **Use Case:** Secure hosting of financial reporting applications
- **Compliance Considerations:** Data isolation, audit trails, controlled access

##  Architecture Diagram
<img width="1364" height="474" alt="vpc dashboard" src="https://github.com/user-attachments/assets/dbf11aba-a23a-48f1-a6de-e79db205a494" />


## 🔧 Architecture Components

| Component | Configuration | Purpose |
|-----------|---------------|---------|
| **VPC** | 10.0.0.0/16 | Isolated network environment |
| **Availability Zones** | 2 AZs | High availability and fault tolerance |
| **Public Subnets** | 10.0.1.0/24 (AZ1), 10.0.2.0/24 (AZ2) | Web/application layer |
| **Private Subnets** | 10.0.3.0/24 (AZ1), 10.0.4.0/24 (AZ2) | Database layer (future) |
| **Internet Gateway** | `project-igw` | Public internet access |
| **Route Tables** | Public → IGW, Private → isolated | Network segmentation |
| **EC2 Instance** | Amazon Linux 2023 (t2.micro) | Application server |
| **Security Group** | `finance-app-sg` | SSH access restricted to my IP |

##  Security Measures Implemented

| Security Control | Implementation | Why It Matters |
|------------------|----------------|----------------|
| **Subnet Segmentation** | Public (app) / Private (db) separation | Database never exposed to internet |
| **No NAT Gateway** | Private subnets have no outbound internet | Reduces attack surface, saves ~$32/month |
| **Least Privilege Access** | Security groups allow only required traffic | Minimizes blast radius |
| **Multi-AZ Deployment** | Resources span 2 AZs | Ensures availability during AZ failures |
| **SSH Key Authentication** | Key-based access, no passwords | Stronger than password authentication |

##  Cost Optimization
- **NAT Gateway not deployed** → Saves approximately **$32/month**
- **t2.micro instance** → Free tier eligible
- **Appropriate sizing** → Matches development/learning needs

##  Deployment Steps

### Prerequisites
- AWS Account
- SSH client (installed)
- Key pair downloaded locally

### 1. VPC Creation
```bash
# VPC: 10.0.0.0/16
# Public subnets: 10.0.0.0/20, 10.0.16.0/20 (EC2 in 10.0.16.0/20)
# Private subnets: 10.0.128.0/20, 10.0.144.0/20

### Database Security Layer
- Created dedicated security group `finance-db-sg.`
- Configured to accept MySQL connections ONLY from EC2 security group
- Implemented security group chaining for zero-trust architecture
**Why this matters: Security group chaining ensures only EC2 instances with the app server 
security group can access the database, even if IP addresses change. So it is more secure, with no IP address management, and it is also scalable (Servers with the same security group will automatically connect or have access to the database in the private subnet.

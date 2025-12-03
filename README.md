# 🛒 Load-Balanced E-Commerce Website on AWS (Using Diagram IPs)

This project demonstrates how to deploy a highly available e-commerce website using **Amazon Web Services (AWS)**. It uses **two EC2 instances**, an **Application Load Balancer (ALB)**, and a custom **VPC with public subnets** — configured exactly as shown in the architecture diagram.

> ✅ **Perfect for beginners learning AWS infrastructure, load balancing, and high availability!**

---

## 🎯 Project Goal
- Deploy a simple e-commerce website on two EC2 instances.
- Distribute incoming web traffic across both instances using an Application Load Balancer.
- Build a secure and scalable network architecture using VPC, subnets, and an Internet Gateway — **matching the provided diagram**.

---

## 🏗️ Architecture Overview 

- **VPC**: `12.0.0.0/16`
- **Public Subnets**:
  - `12.0.1.0/24` → Hosts **EC2 Instance 1**
  - `12.0.3.0/24` → Hosts **EC2 Instance 2**
- **EC2 Instances**: 2 web servers (running Apache)
- **Load Balancer**: Application Load Balancer (HTTP on port 80)
- **Target Group**: Routes traffic to both healthy EC2 instances
- **Internet Gateway**: Enables public internet access

<img width="705" height="417" alt="image" src="https://github.com/user-attachments/assets/240a9d32-2622-4b69-b7b1-2f7715f641d6" />


---

## 🚀 Step-by-Step Implementation

### 1. **Create a Custom VPC (Matching Diagram)**
- Go to **VPC Dashboard → Your VPCs → Create VPC**
  - Name: `ecommerce-vpc`
  - IPv4 CIDR: `12.0.0.0/16` 
- Click **Create**

<img width="1000" height="712" alt="image" src="https://github.com/user-attachments/assets/2a5087b0-7e5d-4638-b149-a0a20c90c9ea" />

---

### 2. **Create Two Public Subnets (As Per Diagram)**
- **Subnet 1**:
  - Name: `public-subnet-1a`
  - VPC: `ecommerce-vpc`
  - Availability Zone: e.g., `us-east-2a`
  - CIDR: `12.0.1.0/24` ← **Matches diagram**
- **Subnet 2**:
  - Name: `public-subnet-2b`
  - AZ: `us-east-2b`
  - CIDR: `12.0.3.0/24` ← **Matches diagram**

<img width="1100" height="756" alt="image" src="https://github.com/user-attachments/assets/901dbcb2-7b67-4320-89b5-b649e54b7a11" />
<img width="1096" height="675" alt="image" src="https://github.com/user-attachments/assets/14c93074-8352-4522-bdd9-b572bec8978f" />



---

### 3. **Create and Attach an Internet Gateway**
- Go to **Internet Gateways → Create**
  - Name: `ecommerce-igw`
- After creation, **attach** it to `ecommerce-vpc`

<img width="954" height="312" alt="image" src="https://github.com/user-attachments/assets/0484d711-6a18-4a36-8c30-6fb79da35186" />
<img width="1103" height="232" alt="image" src="https://github.com/user-attachments/assets/10dbadf7-4ccd-4b90-ab8d-80ff5b60a7dc" />

---

### 4. **Configure Route Table for Public Access**
- Go to **Route Tables**, select the main route table for your VPC
- Edit **Routes** → Add:
  - Destination: `0.0.0.0/0`
  - Target: `ecommerce-igw`
- **Associate** this route table with both public subnets (`12.0.1.0/24` and `12.0.3.0/24`)

<img width="1095" height="357" alt="image" src="https://github.com/user-attachments/assets/f522accc-9c3d-4314-8b2b-a5016644edbd" />
<img width="1168" height="336" alt="image" src="https://github.com/user-attachments/assets/a67d11a3-0b90-4f17-81a3-6d73200d8077" />
<img width="1119" height="239" alt="image" src="https://github.com/user-attachments/assets/fdf4aa92-6ba7-4b35-8942-d8bddf75ac8d" />
<img width="1108" height="291" alt="image" src="https://github.com/user-attachments/assets/185a9dbb-85d2-4ee8-93cb-08516dceca8f" />


---

### 5. **Launch Two Ubuntu EC2 Instances**

Launch **two** EC2 instances using the **Ubuntu 22.04 LTS AMI**:

- **AMI**: `Ubuntu 22.04 LTS (x86_64)` — search in AMI catalog
- **Instance Type**: `t2.micro` (Free Tier eligible)
- **Network**: `ecommerce-vpc`
- **Subnet**:
  - Instance 1 → `public-subnet-1a` (`12.0.1.0/24`)
  - Instance 2 → `public-subnet-1b` (`12.0.3.0/24`)
- **Auto-assign public IP**: ✅ Enable
- **Security Group**: Create new `web-sg` with:
  - **Inbound Rules**:
    - HTTP (Port 80) → Source: `0.0.0.0/0`
    - SSH (Port 22) → Your IP (or `0.0.0.0/0` for testing)

> 📸 *Insert EC2 launch settings showing Ubuntu AMI and subnet selection*

---

### 6. **Deploy Your Website Files on Ubuntu**

After launching, **SSH into each EC2 instance** using your `.pem` key:

```bash
ssh -i "your-key.pem" ubuntu@<public-ip>

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
  - Instance 2 → `public-subnet-2b` (`12.0.3.0/24`)
- **Auto-assign public IP**: ✅ Enable
- **Security Group**: Create new `web-sg` with:
  - **Inbound Rules**:
    - HTTP (Port 80) → Source: `0.0.0.0/0`
    - SSH (Port 22) → Your IP (or `0.0.0.0/0` for testing)
- Paste the script below in the data field of the EC2 instance under "Additional settings" to install Apache2 and update the server:
```
  #!/bin/bash
# Update system packages
apt-get update -y

# Install Apache2
apt-get install -y apache2

# Ensure Apache starts on boot
systemctl enable apache2
systemctl start apache2

# Ensure /var/www/html exists and has correct ownership
mkdir -p /var/www/html
chmod -R 755 /var/www/html

# Optional: Create a simple test page
echo "<h1>Apache2 is running on Server1</h1><p>Instance provisioned automatically on Server1.</p>" > /var/www/html/index.html
```
<img width="917" height="746" alt="image" src="https://github.com/user-attachments/assets/564253e4-7b1d-4974-89fc-4cd265113d88" />
<img width="901" height="618" alt="image" src="https://github.com/user-attachments/assets/25756a6e-7cbc-44c0-b473-6f966111ab85" />
<img width="898" height="671" alt="image" src="https://github.com/user-attachments/assets/c5ae11ad-877f-471a-b6e3-880e2a6ffacf" />
<img width="1398" height="400" alt="image" src="https://github.com/user-attachments/assets/d903c5d5-9970-4b9a-a287-d4c4c6e83ca9" />

<img width="1687" height="194" alt="image" src="https://github.com/user-attachments/assets/169b0ad3-2c1b-4308-8837-e6c93a5a26b8" />


---
## Repeat step 5 to create the second EC2 instance 
- In the echo section of the script, change the h1 tag to Apache2 is running on Server2 to distinguish the servers.
---
**6. Create an Application Load Balancer (ALB)**

- Go to EC2 → Load Balancers → Create Load Balancer
- Choose Application Load Balancer
- Name: `ecommerce-alb`
- Scheme: `Internet-facing`
- IP address type: `IPv4`
- Listeners: `HTTP (Port 80)`
- Availability Zones: Select your VPC and both `public subnets (12.0.1.0/24 and 12.0.3.0/24)`
- Security Group: Create or select `alb-sg` allowing HTTP from `0.0.0.0/0`
- Target Group:
- Create new: `ecommerce-tg`
- Protocol: `HTTP`, `Port: 80`, Health check path: `/`
- Register both Ubuntu EC2 instances by selecting them and click `Include as pending below.`
- Review and Create
<img width="1229" height="757" alt="image" src="https://github.com/user-attachments/assets/e7f344e9-23cc-422c-a43a-5d5c958d5fc3" />
<img width="1372" height="669" alt="image" src="https://github.com/user-attachments/assets/2b791c8e-48b8-444a-a5a1-ae74eece8128" />
<img width="911" height="666" alt="image" src="https://github.com/user-attachments/assets/845a2c8d-76e3-4ba7-94ad-01e3266c7c0b" />
<img width="1314" height="343" alt="image" src="https://github.com/user-attachments/assets/90b723e2-bb47-4de3-9083-ec17ecab798f" />
<img width="1143" height="489" alt="image" src="https://github.com/user-attachments/assets/72fe5e02-d57d-4303-9e12-fc7398296e28" />
<img width="1634" height="734" alt="image" src="https://github.com/user-attachments/assets/faae6c52-7e25-495f-9edf-7e5cab0c2ca9" />
<img width="1458" height="675" alt="image" src="https://github.com/user-attachments/assets/54e105c1-a617-42b9-bb9b-bec963c06c27" />

---
**7. Test the Load Balancer**

- Wait 2–5 minutes for the ALB to become active
- Copy the DNS name of your ALB from the AWS console
- Open in browser: `http://<your-alb-dns-name>`
- Refresh multiple times — you should see alternating messages:
- “Server 1 (Subnet: 12.0.1.0/24)”
- “Server 2 (Subnet: 12.0.3.0/24)”
- Confirm both instances show Healthy in Target Groups
<img width="1402" height="401" alt="image" src="https://github.com/user-attachments/assets/838644ac-9041-4460-803a-2ab0f767508e" />

<img width="1106" height="461" alt="image" src="https://github.com/user-attachments/assets/22d98c2f-7b43-439c-9ccf-f6fe9e01322c" />

---
### 8. **Deploy Your Website Files on Ubuntu**
**Upload your website files (e-commerce web files)**
To upload you website files:

**🔹 Step 1: Open Git Bash in Your Website Folder**
✅ Open your terminal and navigate to your project folder.

```
C:\Users\laolu\Documents\Realprojects\AWS projects\ALB-ecommerce\Electro
```
 **🔹 Step 2: Set Secure Permissions on Your Key**

```
chmod 400 test-ALB-demo.pem
```
**🔹 Step 3: Copy Files to Server 1 (3.139.70.220)**
✅ Run this command (all on one line):

```
scp -i test-ALB-demo.pem -r Electro/* ubuntu@3.139.70.220:/tmp/
```
**🔹 Step 4: Move Files into Web Folder on Server 1**
✅ Now log in to Server 1:

```
ssh -i test-ALB-demo.pem ubuntu@3.139.70.220
```
✅ Once logged in, run these 3 commands (copy/paste one at a time):

```
# 1. Delete default Apache files
sudo rm -rf /var/www/html/*
```

```
# 2. Move your files from /tmp to the web folder
sudo mv /tmp/* /var/www/html/
```
**🔹 Step 5: Copy Files to Server 2 (18.217.149.70)**
✅ Run this command (all on one line):

```
scp -i test-ALB-demo.pem -r Electro/* ubuntu@18.217.149.70:/tmp/
```
✅ Wait for the files to finish copying.

```
🔹 Step 6: Move Files into Web Folder on Server 2
```
✅ Log in to Server 2:

```
ssh -i test-ALB-demo.pem ubuntu@18.217.149.70
```
✅ Run the same 3 commands:

```
sudo rm -rf /var/www/html/*
sudo mv /tmp/* /var/www/html/
exit

```
✅ Done! 🎉
Your entire E-commerce website is now live on both servers.

🔍 Test It Out
- Open your browser and go to:
- ➤ `http://3.139.70.220` → should show your real website
- ➤ `http://18.217.149.70` → same website

- From your Load Balancer, visit its DNS name and refresh — you should see the same site served from both servers.
- Load Balancer DNS: `http://ecommerce-alb-178280318.us-east-2.elb.amazonaws.com/`

<img width="1649" height="967" alt="image" src="https://github.com/user-attachments/assets/cc9412ec-41f2-4647-803e-78f6b2a5ef4c" />
<img width="1659" height="814" alt="image" src="https://github.com/user-attachments/assets/229083e8-2341-431b-9748-bc42c759a7c9" />
<img width="1638" height="532" alt="image" src="https://github.com/user-attachments/assets/23bb884e-0600-421c-961d-ee0e7bbaaa08" />
<img width="1651" height="505" alt="image" src="https://github.com/user-attachments/assets/b2043638-b539-4b83-9a8a-a17c1615013a" />
<img width="1620" height="782" alt="image" src="https://github.com/user-attachments/assets/cb802dc9-d8f5-42ff-b5bc-339af3f5612f" />



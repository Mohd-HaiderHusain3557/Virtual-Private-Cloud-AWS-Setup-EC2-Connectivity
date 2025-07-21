# Virtual-Private-Cloud-AWS-Setup-EC2-Connectivity
A Virtual Private Cloud (VPC) is a logically isolated section of the AWS cloud where you can launch and manage AWS resources (like EC2 instances, RDS databases, etc.) in a custom-defined virtual network.
# 🛠️ AWS VPC Setup & EC2 Deployment Guide

This guide provides a step-by-step walkthrough for creating a custom Virtual Private Cloud (VPC) in AWS and launching an EC2 instance within it. It includes the setup of subnets, route tables, internet access, and verification.

---

## 📌 Prerequisites

- AWS Account
- Key Pair for EC2 login
- Basic understanding of VPC components (subnets, IGW, route tables, etc.)

---

## 🚀 Steps

### **Step 1: Create a New VPC**
- Go to **VPC Dashboard** in AWS Console.
- Click **“Create VPC”** → choose **VPC Only**.
- Name your VPC (e.g., `MyCustomVPC`).
- Set IPv4 CIDR block: `10.0.0.0/16`.
- Leave other options default and click **Create VPC**.

---

### **Step 2: Create Subnets**
- Navigate to **Subnets** → Click **Create Subnet**.
- Choose the created VPC.
- Add:
  - `Public Subnet`: `10.0.1.0/24`
  - `Private Subnet`: `10.0.2.0/24`
- Use different **Availability Zones** for high availability.

---

### **Step 3: Create and Attach Internet Gateway**
- Go to **Internet Gateways** → Click **Create internet gateway**.
- Name it and **Attach to VPC** (your custom VPC).

---

### **Step 4: Create Route Table for Public Subnet**
- Go to **Route Tables** → Click **Create route table**.
- Associate it with your custom VPC.
- Add a route:
  - Destination: `0.0.0.0/0`
  - Target: **Internet Gateway**
- Under **Subnet Associations**, associate it with the **Public Subnet**.

---

### **Step 5: (Optional) Create NAT Gateway**
- Go to **NAT Gateways**.
- Select the **Public Subnet**.
- Allocate a new **Elastic IP** and create the gateway.

---

### **Step 6: Create Route Table for Private Subnet**(optional)
- Create another route table and associate it with the VPC.
- Add a route to `0.0.0.0/0` pointing to the **NAT Gateway**.
- Associate this table with the **Private Subnet**.

---

### **Step 7: Launch EC2 Instance in Public Subnet**
- Go to **EC2 > Launch Instance**.
- Select an AMI (e.g., Amazon Linux).
- Choose instance type (`t2.micro`).
- In **Network Settings**:
  - Choose your custom **VPC**
  - Select **Public Subnet**
  - Enable **Auto-assign Public IP**
- Use an existing or new key pair.
- Click **Launch**.

---

### **Step 8: Modify EC2 Security Group** (optional)
- Go to **Security Groups**.
- Edit inbound rules:
  - **SSH (port 22)** – from your IP.
  - **HTTP (port 80)** – if hosting a web server.
  - Any other required ports.

---

### **Step 9: and 10: make sure you select - your VPC created in the routing table then confirm all setup
---
## Step 11: Attach Internet Gateway to VPC

To allow instances in your public subnet to access the internet:

1. Navigate to **VPC Dashboard → Internet Gateways**
2. Click **Create Internet Gateway**
   - Enter a name (e.g., `igw01`)
   - Click **Create internet gateway**
3. Once created, select the IGW
4. Click **Actions → Attach to VPC**
5. Choose your VPC (e.g., `vpc01`)
6. Click **Attach internet gateway**

> This links your VPC to the internet—essential for enabling public access to EC2 instances in public subnets.
---  



### 🛣️ Step 12: Add a Route to Enable Internet Access (Public Subnet)

In the Route Table associated with your public subnet:

- Click **Add route**.
- Set **Destination**: `0.0.0.0/0`
- Set **Target**: Your Internet Gateway (e.g., `igw-xxxxxxxx`)
- Click **Save changes**

This enables outbound internet access from instances in the public subnet.

---

###  Step 13: Add a Route for NAT Access (Private Subnet) (optional)

In the Route Table associated with your private subnet:

- Click **Add route**
- Set **Destination**: `0.0.0.0/0`
- Set **Target**: Your NAT Gateway (e.g., `nat-xxxxxxxx`)
- Click **Save changes**

This allows private subnet instances to access the internet securely via NAT.

---

###  Step 14: Associate Subnet with Route Table

To apply routing rules to your subnet:

- Go to **VPC → Route Tables**
- Select your target route table (e.g., `rtb-0ea94b92a1a57e4b8`)
- Click **Edit subnet associations**
- Select subnet (e.g., `subnet01`)
- Click **Save associations**

Your subnet now follows the rules defined in the route table.

---

###  Step 15: Launch EC2 Instance with Windows AMI

To launch a Windows EC2 instance:

- Navigate to **EC2 → Launch Instance**
- Enter name: `server1`
- Select AMI: **Microsoft Windows Server 2025**
- Instance type: `t3.micro` (or preferred type)
- Security Group: Allow **RDP (3389)** for remote access
- Auto-assign Public IP: **Enabled**
- Storage: Modify volume size (e.g., 30 GiB)
- Click **Launch instance**

---

###  Step 16: Configure Network Settings for EC2

While launching:

- VPC: Select `vpc01` (`vpc-025cb5e7c1d402f6a`)
- Subnet: Select `subnet01` (`subnet-09f80057c55ef72e1`)
- Ensure Auto-assign Public IP is **Enabled**
- Security Group:
  - Example: `sg1` (created `2025-07-21T15:39`)
  - Inbound Rules: Allow **All traffic** temporarily for setup
- Review summary and launch instance

> ⚠️ Restrict security group rules after testing to minimize exposure.

---

###  Step 17: Connect to EC2 Instance via RDP Client

Once instance is running:

- Go to EC2 Dashboard → Select instance
- Click **Connect → RDP client**
- Note Public IP: `43.205.120.187`
- Click **Download .rdp file**
- Click **Get Password** → Decrypt using `.pem` key
- Use RDP client → Enter username `Administrator` and decrypted password

---

###  Step 18: Authenticate and Access EC2 via RDP

In the RDP login prompt:

- Username auto-filled (e.g., `Administrator`)
- Paste decrypted password
- Click **OK**
- Accept certificate warning if prompted
- Session initiates → Windows desktop appears

You’re now successfully connected to your Windows EC2 instance!



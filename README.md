# Two-Tier-Architecture-on-AWS-using-VPC
Secure two-tier AWS architecture with an app server in the public subnet and a DB server in the private subnet using VPC.

## About the Project:

This project sets up a custom Virtual Private Cloud (VPC) in AWS with two subnets:

- A **public subnet** hosting an EC2 app server accessible via the internet.
- A **private subnet** hosting a DB server accessible only via SSH from the app server.

It demonstrates secure cloud architecture using route tables, security groups, and subnet design—ideal for students, backend developers, and cloud beginners.

## Technologies Used:

- **AWS VPC** – Isolated cloud network to host resources securely
- **EC2** – Virtual servers to run applications and databases
- **Subnets** – Logical divisions of VPC for public/private access
- **Internet Gateway** – Enables internet access for public subnet
- **Route Tables** – Controls traffic flow within VPC
- **Security Groups** – Firewall rules for EC2 instances
- **SSH** – Secure protocol to access servers remotely
- **IAM (optional)** – Manages user permissions and roles in AWS

## Prerequisites:

- AWS Free Tier account
- Basic knowledge of networking (IP, CIDR)
- Familiarity with EC2 and AWS Console
- SSH key pair for EC2 login
- Optional: AWS CLI or Terraform for automation

---

## What is VPC?

A **Virtual Private Cloud** is a logically isolated section of AWS where you can launch resources in a defined network. You control IP ranges, subnets, route tables, and gateways.

## What is a Subnet?

A **Subnet** is a segment of a VPC’s IP range.

- **Public Subnet**: Connected to the internet via an Internet Gateway
- **Private Subnet**: Isolated, no direct internet access

## What is an Internet Gateway?

An **IGW** allows communication between your VPC and the internet. It’s attached to the VPC and used in route tables for public access.

## What is a Route Table?

A **Route Table** defines how traffic flows within your VPC.

- Public route table: routes 0.0.0.0/0 to IGW
- Private route table: no internet route (or via NAT if needed)

## What is a NAT Gateway?

A **NAT Gateway** allows instances in a private subnet to access the internet **without being exposed** to incoming traffic.
(Not used in this project, but useful for updates or outbound access)

## What is a Security Group?

A **Security Group** acts as a virtual firewall for EC2 instances.

- App SG: allows SSH, HTTP
- DB SG: allows SSH/MySQL only from App SG

---

## Step 1: Create a VPC

1. Go to AWS console → Open VPC Service 
2. Click on Create VPC 
    - Select → VPC only
    - Name → demo-vpc
    - **IPv4 CIDR → 10.0.0.0/16**
    - Click on Create VPC

![Project Screenshot](/images/vpc-create1.png)
![Project Screenshot](/images/vpc-create2.png)
![Project Screenshot](/images/vpc-create3.png)
![Project Screenshot](/images/vpc-done.png)

## Step 2: Create a Subnet’s

1. Go to Subnet Option 
2. Click on Create Subnet 
    - Select VPC → demp-vpc

![Project Screenshot](/images/subnet-create1.png)
![Project Screenshot](/images/subnet-create2.png)

- Create Subnet 1
    - Name → Public Subnet
    - Availability Zone → Asia Pacific (Mumbai) / aps1-az1 (ap-south-1a)
    - IPv4 subnet CIDR block → 10.0.0.0/20

![Project Screenshot](/images/subnet-pub-south-1a.png)

- **Add new subnet**
- Create Subnet 2
    - Name → Private Subnet
    - Availability Zone → Asia Pacific (Mumbai) / aps1-az3 (ap-south-1b)
    - IPv4 subnet CIDR block → 10.0.16.0/20

![Project Screenshot](/images/subnet-pri-south-1b.png)
![Project Screenshot](/images/subnet-done.png)

3. Public Subnet to set public IP 
    - Select Public Subnet
    - Click Action → Edit subnet settings
    - Check-in → Auto-assign IP settings
    - Click Save Changes

![Project Screenshot](/images/edit-pub-subnet1.png)
![Project Screenshot](/images/edit-pub-subnet2.png)

## Step 3: Launch EC2 Instances

1. Go to AWS Console → EC2

![Project Screenshot](/images/launch-instance.png)

2. Click on Create Instance 
    - Name → **app_server**
    - Choose AMI → Amazon Linux.
    - Instance type → t2.micro
    - Key pair → vpc-mumbai-pem-key
    - Network settings Edit
    - Select VPC → demo-vpc
    - Subnet → Public-Subnet
    - Auto-assign public IP → **Enable**
    - security group → VPC-SG-1 (port: ssh-22, http-80, https-443)

![Project Screenshot](/images/instance-app1.png)
![Project Screenshot](/images/instance-app2.png)

3. Click on Create Instance 
    - Name → **db_server**
    - Choose AMI → Amazon Linux.
    - Instance type → t2.micro
    - Key pair → vpc-mumbai-pem-key
    - Network settings Edit
    - Select VPC → demo-vpc
    - Subnet → Private-Subnet
    - Auto-assign public IP → **Disable**
    - security group → VPC-SG-2 (port: ssh-22, mysql-3306)
    - Click on Create

![Project Screenshot](/images/instance-db1.png)
![Project Screenshot](/images/instance-db1.png)
![Project Screenshot](/images/instance-done.png)

## Step 4: Create Internet Gateway

1. Go to VPC service → Click on Internet Gateway Tab

![Project Screenshot](/images/IGW-create1.png) 

2. Click on **Create internet gateway**
    - Name → IGW
    - Click on Create internet gateway
3. Select internet gateway

![Project Screenshot](/images/IGW-create2.png) 
![Project Screenshot](/images/IGW-create3.png) 

4. Click on Action → Attach to VPC 
    - Select VPC → demo-vpc
    - click on Attach internet gateway

![Project Screenshot](/images/IGW-create4.png) 

5. IGW - Entry on Route Table
    - Click on Route Table Tab
    - Select your Route Table (Public-RT)
    - Click To Routes → Edit Routes
    - Add Route → Select internet gateway → Select IGW-current your
    - Click on Save Changes
6. Your Internet Gateway is Created 

![Project Screenshot](/images/IGW-Public-RT1.png)
![Project Screenshot](/images/IGW-Public-RT2.png)

## Step 5: Create NAT Gateway

1. Click on the NAT Gateway Tab 
2. Click Create NAT Gateway
    - NAT gateway settings
    - Name → NAT
    - Subnet → Public-Subnet
    - Elastic IP → Click on **Allocate Elastic IP**
    - Click on Create NAT Gateway

![Project Screenshot](/images/NAT-create1.png)
![Project Screenshot](/images/NAT-create2.png)

3. Create a Private Route Table 
    - Click on Route Table Tab
    - Click on Create Route Table
    - Name → Private-RT
    - Click on Save Changes
4. NAT - Entry on Route Table
    - Click on Route Table Tab
    - Select your Route Table (Private-RT)
    - Click To Routes → Edit Routes
    - Add Route →Select NAT-current your
    - Click on Save Changes
5. Edit Subnet Associations
    - Click on Route Table Tab
    - Select your Route Table (Private-RT)
    - Click To Routes → Edit Subnet Associations
    - Select Private-Subnet
    - Click on Save Changes

![Project Screenshot](/images/NAT-Private-RT1.png)
![Project Screenshot](/images/NAT-Private-RT2.png)
![Project Screenshot](/images/NAT-Private-RT3.png)
![Project Screenshot](/images/NAT-Private-RT4.png)
![Project Screenshot](/images/NAT-Private-RT5.png)
![Project Screenshot](/images/NAT-Private-RT6.png)

## Step 6: Connect via SSH

1. connect o app server

```bash
ssh -i "vpc-mumbai-pem-key.pem" ec2-user@3.110.210.173
```

2. hostname command to set name

```bash
sudo hostnamectl hostname app-server
```

3. Re Connect 

![Project Screenshot](/images/ssh-app.png) 

4. now update system then install and start nginx 

```bash
sudo yum update
sudo yum install nginx -y 
sudo systemctl start nginx 
sudo systemctl enable nginx 
```

5. Default index.html file update 

```bash
cd /usr/share/nginx/html/
ls
vim index.html 
#all contain remove and paste this line 
#echo "<h1>Hello!! This is $(hostname -f) running from the VPC inside the public subnet</h1>" | sudo tee /usr/share/nginx/html/index.html > /dev/null
#restart nginx 
sudo systemctl restart nginx
```

6. Run Your app-server 

```bash
#enter ip to run 
http://3.110.210.173/
```

![Project Screenshot](/images/app-server-runing.png)

7. Copy key via scp command used to local machine to app-server 

```bash
 scp -i /c/ssh-keys/vpc-mumbai-pem-key.pem /c/ssh-keys/vpc-mumbai-pem-key.pem ec2-user@3.110.210.173:/home/ec2-user/
```

![Project Screenshot](/images/scp-key.png)

8. check key copied 

```bash
ls
#vpc-mumbai-pem-key.pem
```

9. Connect **db-server via ssh**

```bash
sudo ssh -i vpc-mumbai-pem-key.pem ec2-user@10.0.30.104
```

10. hostname command to set name

```bash
sudo hostnamectl hostname db-server
```

11. re connect then check internet access or not 

```bash
#checkin only internet connecttion
ping google.com
```

![Project Screenshot](/images/ssh-db.png)
![Project Screenshot](/images/ssh-db-update.png)

## Step 7: Delete All instance, NAT, IGW, & VPC

1. Go to EC2 service 
2. Select instance you want to deleted 
3. click on the **Instance state →** Click on Terminate instance 

![Project Screenshot](/images/instance-delete.png)

4. Go to VPC service 
5. Click on NAT Gateway → Select NAT 
6. Click on Delete NAT Gateway 

![Project Screenshot](/images/NAT-delete.png)

7. Click on **Elastic IPs →** Select IP 
8. Click on Release Elastic IP addresses → Click Release 

![Project Screenshot](/images/Elastic-ip-delete.png)

9. Click on Internet Gateway → Select which are delete 
10. Then Click on Action → Detach 1st

![Project Screenshot](/images/detach-IGW.png)
![Project Screenshot](/images/delete-IGW.png)

11. Re-select then click Action → Delete Internet Gateway 
12. Go to VPC → Select VPC 
13. Click on Action → select **Delete VPC**
14. You deleted VPC then automatically 5 resources will also be deleted
    - security group of vpc, public & private Subnet, then also public & private Route table
15. type delete then Click on Delete

![Project Screenshot](/images/delete-VPC.png)

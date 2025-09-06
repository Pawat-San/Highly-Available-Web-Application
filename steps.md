# Steps: Highly Available Web Application

## ✅ Prerequisites
- AWS Account
- VPC with Public Subnets
- Security Group allowing SSH (22) and HTTP (80)
- Key Pair for EC2 access

---

## Step 1: Create Launch Template
1. Go to **EC2 Console → Launch Templates → Create launch template**
2. Name: `WebServerTemplate`
3. AMI: Amazon Linux 2
4. Instance type: `t2.micro`
5. Security Group: Allow SSH + HTTP
6. User data:
   ```bash
   #!/bin/bash
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd
   echo "Hello from Auto Scaling Instance" > /var/www/html/index.html

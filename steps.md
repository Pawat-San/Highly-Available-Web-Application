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
# Steps: Highly Available Web Application

---

## Step 2: Create Auto Scaling Group (ASG)
1. Go to **EC2 Console → Auto Scaling Groups → Create Auto Scaling group**
2. Name: `WebServerASG`
3. Select Launch Template: `WebServerTemplate`
4. Network: Select VPC + 2 Public Subnets
5. Attach Load Balancer:
   - Choose existing ALB (or create new one)
   - Register Target Group
6. Desired capacity: `2`
7. Min: `2`
8. Max: `4`
9. Create ASG

---

## Step 3: Create Application Load Balancer
1. Go to **EC2 → Load Balancers → Create Application Load Balancer**
2. Name: `MyALB`
3. Internet-facing, IPv4
4. Select VPC and at least 2 Public Subnets
5. Listener: HTTP (80) → Forward to Target Group
6. Security Group: Allow HTTP
7. Create

---

## Step 4: Create CloudWatch Alarm
1. Go to **CloudWatch → Alarms → Create Alarm**
2. Metric: `EC2 → CPU Utilization (ASG instances)`
3. Condition: Greater than **70% for 2 periods**
4. Action: **Auto Scaling policy** (Scale out by +1 instance)
5. Create Alarm

---

## Step 5: Create SNS Topic
1. Go to **SNS Console → Topics → Create topic**
2. Type: Standard
3. Name: `WebAppAlerts`
4. Create
5. Add **Subscription → Email**
6. Confirm subscription via email link

---

## Step 6: Test Scaling & Alerts
1. SSH into one instance
2. Install stress tool:
   ```bash
   sudo amazon-linux-extras install epel -y
   sudo yum install -y stress
   stress --cpu 2 --timeout 300

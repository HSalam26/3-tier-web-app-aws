# Deployment Guide for 3-Tier Web App on AWS

This guide provides step-by-step instructions for deploying a 3-tier web application on AWS using Amazon EC2, Auto Scaling Groups, and Amazon RDS.

## Prerequisites

- An AWS account with appropriate permissions to create and manage resources.
- Basic knowledge of AWS services and concepts.
- AWS CLI configured on your local machine (optional but recommended).

## Step 1: Setup VPC, Subnets, Internet Gateway, and NAT Gateway

1. Open the AWS Management Console and navigate to the VPC service.
2. Create a new VPC with an IPV4 CIDR block of `10.0.0.0/16`.
3. Within the VPC, create 2 availability zones (AZs) for high availability.
4. Create 2 public subnets and 4 private subnets, distributing them across the AZs.
5. Enable auto-assigning of IPv4 addresses for the public subnets.
6. Attach an internet gateway to the VPC to enable internet access.
7. Create a NAT gateway in one of the public subnets to enable outbound internet access for private subnets.

## Step 2: Deploy EC2 Instances using Launch Templates and Auto Scaling Groups

1. Open the Amazon EC2 service in the AWS Management Console.
2. Create a security group that allows inbound traffic on port 22 (SSH) and port 80 (HTTP).
3. Create an EC2 launch template for the web-tier with the following details:
   - Specify the created security group.
   - Add user data with the following script:
     ```
     #!/bin/bash
     sudo yum update -y
     sudo yum install -y httpd
     sudo systemctl start httpd
     sudo systemctl enable httpd
     echo "<html><body><h1>You made it to this section, keep going</h1></body></html>" > /var/www/html/index.html
     ```
4. Create another launch template for the application tier without the user data script.
5. Create two Auto Scaling Groups (ASGs) for the web and application tiers respectively:
   - Configure the web ASG to use the web-tier launch template and the public subnets.
   - Configure the application ASG to use the application-tier launch template and the private subnets.

## Step 3: Create Database

1. Go to the Amazon RDS service in the AWS Management Console.
2. Create a subnet group using the remaining private subnets.
3. Create a database (e.g., MySQL, PostgreSQL) using the appropriate template within the RDS service.

## Step 4: Test out Public and Private EC2 Instances

1. Locate the public IP addresses of the instances in the web-tier Auto Scaling Group.
2. Access the web-tier instances using their public IP addresses in a web browser to confirm the deployment.
3. Connect to the application-tier instances via SSH using the private IP addresses to verify their accessibility.

## Conclusion

Congratulations! You have successfully deployed a 3-tier web application on AWS using EC2, Auto Scaling Groups, and RDS. Please follow the instructions carefully and ensure you have the necessary permissions and resources in place before proceeding with the deployment.



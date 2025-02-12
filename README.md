Documentation for Deploying a Web Application Using Auto Scaling Group (ASG) and Application Load Balancer (ALB) on AWS

Overview

This documentation provides a detailed guide to deploying a scalable and fault-tolerant web application on AWS using:

Application Load Balancer (ALB): Distributes incoming traffic across multiple targets (e.g., EC2 instances) in different Availability Zones.

Auto Scaling Group (ASG): Automatically adjusts the number of EC2 instances based on traffic demand, ensuring high availability and cost efficiency.

The setup includes a dynamic scaling policy based on CPU Utilization, configured using CloudWatch Alarms, SNS, and IAM.


Prerequisites

AWS Account: Ensure you have access to an AWS account with permissions to manage EC2, ALB, IAM, and CloudWatch resources.

AWS CLI: Installed and configured on your local machine.

Git: Installed locally to clone the repository.

Steps to Deploy the Web Application

1. Create an IAM User

Create an IAM user (e.g., adminjohn) with Administrator Access to manage AWS resources.

2. Security Groups

Create separate security groups for:

Launch Template (LT): Allow HTTP (port 80) and SSH (port 22) traffic.

Application Load Balancer (ALB): Allow inbound HTTP (port 80) and HTTPS (port 443) traffic.

3. Launch Template (LT) and Auto Scaling Group (ASG)

Create a Launch Template:

Go to the EC2 Dashboard > Launch Templates.

Click Create Launch Template.

Specify the required configuration:

AMI ID (use a base Amazon Linux 2 AMI initially).

Instance type (e.g., t2.micro).

Key pair for SSH access.

User data (see below for WebApp setup).

Launch an EC2 Instance from the Launch Template:

SSH into the instance.

Configure the WebApp and create a custom AMI.

4. WebApp Configuration

Run the following commands after SSH into the instance:

sudo su -
yum install httpd -y

git clone https://github.com/StratusDev/AWS-WebApp-Deployment.git

cp -r AWS-WebApp-Deployment/ /var/www/html

systemctl start httpd

systemctl enable httpd

5. Register a Custom AMI

After configuring the WebApp, create a custom AMI to include the application setup.

6. Update Launch Template with New AMI

Modify the Launch Template to use the newly created AMI.

7. Auto Scaling Group (ASG) Instance Refresh

Perform an instance refresh in the ASG to deploy new instances using the updated AMI.

8. Target Group (TG) and Application Load Balancer (ALB)

Create a Target Group:

Choose Instances as the target type.

Configure health checks (HTTP path / with port 80).

Create an ALB:

Attach the target group.

Define listeners for HTTP and HTTPS traffic.

9. Attach ALB to ASG

Associate the ALB with the ASG to distribute traffic across instances.

10. Dynamic Scaling Policy

Create CloudWatch Alarms:

Alarm for high CPU utilization (e.g., > 70%).

Alarm for low CPU utilization (e.g., < 20%).

Create an SNS Topic:

Subscribe an email address to receive scaling notifications.

Attach Scaling Policies:

Configure scale-out and scale-in policies based on the CloudWatch Alarms.

11. Run CPU Stress Test

Install and configure the stress utility on Amazon Linux to simulate high CPU usage:

sudo amazon-linux-extras install epel -y
yum install stress -y
stress --cpu 1 --timeout 800 &

Verify that the scaling events are triggered as expected.

Monitoring and Logging

CloudWatch Metrics:

Monitor key metrics like CPU utilization, request count, and instance health.

Logs:

Collect application logs using CloudWatch Logs or store them in S3.

Troubleshooting

Scaling Issues:

Verify scaling policies and CloudWatch Alarm configurations.

Health Check Failures:

Ensure the health check path is correctly configured.

ALB Access:

Confirm the ALB DNS name is accessible.


Architectural Diagram:-

![image](https://github.com/user-attachments/assets/5aac1d58-c5d7-45f1-bc97-a44929d7df70)



AWS SNS:


![image](https://github.com/user-attachments/assets/c3cb6784-7946-49e7-9c36-08b972674d86)



AWS CloudWatch:


![image](https://github.com/user-attachments/assets/a4b5f5a3-a02f-4281-b5b7-aaa322041224)





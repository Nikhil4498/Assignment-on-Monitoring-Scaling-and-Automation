# Step-by-Step Procedure: Web Application Lifecycle Management with EC2, Auto Scaling, and SNS Notifications

## Overview
This document provides a comprehensive guide to deploying and managing a simple HTML web application on AWS. The lifecycle includes EC2 instance deployment, setting up an Application Load Balancer (ALB), configuring Auto Scaling, and implementing SNS notifications.

### Prerequisites
1. An active AWS account.
2. AWS CLI installed and configured with appropriate permissions.
3. Basic knowledge of Linux and networking.

---

## 1. Create and Deploy a Simple HTML Web Application

### Steps:
1. **Prepare Your HTML Application:**
    - Create `index.html` and `styles.css`:
      ```html
      <!DOCTYPE html>
      <html lang="en">
      <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <link rel="stylesheet" href="styles.css">
          <title>Simple Web App</title>
      </head>
      <body>
          <h1>Welcome to My Simple Web App</h1>
          <p>This is a demo application hosted on AWS.</p>
      </body>
      </html>
      ```
      ```css
      body {
          font-family: Arial, sans-serif;
          text-align: center;
          background-color: #f4f4f4;
      }
      h1 {
          color: #333;
      }
      p {
          color: #666;
      }
      ```
2. **Transfer Files to EC2:**
    - Use SCP to upload the files to your EC2 instance:
      ```bash
      scp -i "your-key.pem" index.html styles.css ubuntu@<your-ec2-public-ip>:/home/ubuntu/
      ```

3. **Install and Configure a Web Server on EC2:**
    - SSH into your EC2 instance:
      ```bash
      ssh -i "your-key.pem" ubuntu@<your-ec2-public-ip>
      ```
    - Install Nginx:
      ```bash
      sudo apt update && sudo apt install nginx -y
      ```
    - Move files to the Nginx root directory:
      ```bash
      sudo mv /home/ubuntu/index.html /home/ubuntu/styles.css /var/www/html/
      ```
    - Restart Nginx:
      ```bash
      sudo systemctl restart nginx
      ```

---

## 2. Configure Load Balancer and Auto Scaling

### Steps:

1. **Launch Additional EC2 Instances:**
    - Repeat the steps in Section 1 to deploy the application on multiple EC2 instances.

2. **Set Up an Application Load Balancer (ALB):**
    - Navigate to the AWS Management Console > EC2 > Load Balancers.
    - Create a new ALB and configure:
      - **Target Group:** Register your EC2 instances.
      - **Listener:** Forward HTTP (Port 80) traffic to the target group.

3. **Configure Auto Scaling:**
    - Go to EC2 > Auto Scaling Groups.
    - Create an Auto Scaling Group with:
      - Launch template: Include an EC2 instance configuration.
      - Scaling policies: Define policies based on CPU utilization.

---

## 3. Implement SNS Notifications

### Steps:
1. **Set Up SNS Topic:**
    - Navigate to the SNS dashboard.
    - Create a topic and subscribe your email.

2. **Create CloudWatch Alarms:**
    - Go to CloudWatch > Alarms.
    - Set up alarms for:
      - High CPU utilization (trigger scale-out).
      - Low CPU utilization (trigger scale-in).
    - Link these alarms to the Auto Scaling policies and the SNS topic.

---

## 4. Automate Infrastructure Deployment with Boto3

### Python Script:
```python
import boto3

def deploy_web_app():
    ec2 = boto3.client('ec2')
    elb = boto3.client('elbv2')
    asg = boto3.client('autoscaling')
    sns = boto3.client('sns')

    # Example: Create S3 bucket for static files (optional)
    s3 = boto3.client('s3')
    bucket_name = 'your-bucket-name'
    s3.create_bucket(Bucket=bucket_name)

    # Steps to automate EC2, ALB, ASG, and SNS setup go here...
    print("Deployment complete!")

if __name__ == "__main__":
    deploy_web_app()
```



## ✅ NGINX EC2 Web Server Provisioning Using AWS CLI / Terraform

### 🔧 Provisioned Infrastructure

* **VPC**: `10.0.0.0/16`
* **Public Subnet**: Created inside the VPC
* **Security Group Rules**:

  * Port **22 (SSH)**: Open to all (0.0.0.0/0)
  * Port **80 (HTTP)**: Open to all (0.0.0.0/0)
* **EC2 Instance**:

  * AMI: Amazon Linux 2
  * User Data Script: Installs and runs NGINX, sets custom HTML content

---






## Installation
### 🖥️ Commands Run Inside EC2 Instance (from screenshots)

```bash
# Check NGINX status
sudo systemctl status nginx

# Enable NGINX to start on boot
sudo systemctl enable nginx

# Set custom content in default HTML page
echo "<h1> Hello From Devops </h1>" | sudo tee /usr/share/nginx/html/index.html
```


>
> * `usr/share/nginx/index.html` 
               OR
`/usr/share/nginx/html/index.html`

## Output and Screenshot


### 📤 Outputs

| Resource  | Value                                                           |
| --------- | --------------------------------------------------------------- |
| Public IP | `54.196.229 17`                                                 |

---

![Screenshot 2025-05-07 114002](https://github.com/user-attachments/assets/23b13dd5-e843-481a-b1d0-6e354f63a794)
![Screenshot 2025-05-07 113535](https://github.com/user-attachments/assets/ad6b4715-13f7-4797-aa31-cf8b01a5d3ee)


# AWS EC2 Health Monitoring Automation 

## 📌 Project Overview

**Objective**: Create an automated system to monitor EC2 instance health and log results into an S3 bucket.

**Key Problem**: Manual health checks become inefficient and error-prone as the number of EC2 instances increases.

---

## ⚙️ Manual vs. Automated Health Checks

| Approach        | Manual Checks                                  | Automated Checks                                      |
|----------------|------------------------------------------------|--------------------------------------------------------|
| Access          | Log into AWS console individually              | Uses AWS Lambda function                              |
| Status Check    | Manually check each instance                   | Automatically checks health via describe_instance_status API |
| Time Efficiency | Time-consuming                                 | Scheduled regularly using Amazon EventBridge/CloudWatch |
| Reliability     | Error-prone                                     | Reliable and consistent                              |
| Logging         | No central logging                              | Saves logs to S3 with timestamps                     |

---

## 🚀 Project Implementation Steps

1. **Set up EC2 Instance**
2. **Create S3 Bucket**
3. **Create IAM Role**
4. **Develop AWS Lambda Function**
5. **Test Lambda Function**
6. **Configure EventBridge**
7. **Log Health Data in S3**

---

## 🖥️ EC2 Instance Setup

- **Platform**: Amazon Linux
- **Instance Type**: t2.micro
- **Configuration**:
  - Create key pair
  - Use default security group
  - Configure network settings

---

## 🗃️ S3 Bucket Setup

- **Purpose**: Store EC2 health status logs centrally
- **Type**: General-purpose S3 bucket
- **Configuration**:
  - Unique bucket name
  - Disable public access
  - Enable bucket versioning

---

## 🔐 IAM Role Configuration

- **Trusted Entity**: AWS Lambda
- **Permissions**:
  - `AmazonEC2ReadOnlyAccess`
  - `AmazonS3FullAccess`

---

## 🧠 Lambda Function Details

- **Language**: Python 3.12
- **Functionality**:
  - Calls `describe_instance_status` from Boto3
  - Extracts and formats:
    - Instance ID
    - State (Running/Stopped)
    - Health Status
    - Timestamp
  - Uploads log to S3

---
## Python Script for EC2 Health Monitoring :

import boto3
import datetime

ec2 = boto3.client('ec2')
s3 = boto3.client('s3')

BUCKET_NAME = '11 mybucket1'  

def lambda_handler(event, context):
    response = ec2.describe_instance_status(IncludeAllInstances=True)
    
    now = datetime.datetime.utcnow().isoformat()
    logs = []

    for instance in response['InstanceStatuses']:
        instance_id = instance['InstanceId']
        state = instance['InstanceState']['Name']
        status = instance['InstanceStatus']['Status']
        system_status = instance['SystemStatus']['Status']
        
        line = f"{now}, {instance_id}, {state}, Instance: {status}, System: {system_status}"
        logs.append(line)
    
    log_data = "\n".join(logs)
    file_name = f"ec2-health-checks/log_{now}.txt"

    s3.put_object(Bucket=BUCKET_NAME, Key=file_name, Body=log_data.encode('utf-8'))
    
    return {
        'statusCode': 200,
        'body': f"Health check logged to {file_name}"
    }
## ⏱️ Scheduling and Automation

- **Tool**: Amazon EventBridge
- **Interval**: Configurable (e.g., every 1 hour)
- **Target**: Lambda Function

---
## screenshots

![Screenshot 2025-05-07 115728](https://github.com/user-attachments/assets/0c65ba1f-f4a3-472a-8119-bc51614e81bd)
![Screenshot 2025-05-07 120033](https://github.com/user-attachments/assets/4bd9e88c-30f6-4077-9a0e-fdfafe0501a7)
![Screenshot 2025-05-07 120055](https://github.com/user-attachments/assets/64067363-bd9a-4404-85cc-98332ead7cfd)
![Screenshot 2025-05-07 120929](https://github.com/user-attachments/assets/8c8ce238-e181-4824-ab8f-b419e17ebd7e)
![Screenshot 2025-05-07 122029](https://github.com/user-attachments/assets/89090d60-2ae9-474f-9231-485aebe0fb96)
![Screenshot 2025-05-07 122029](https://github.com/user-attachments/assets/70db1d93-7e81-419e-821c-a27c4969de0f)

[log_2025-05-07T06_39_26.932543.txt](https://github.com/user-attachments/files/20080867/log_2025-05-07T06_39_26.932543.txt)
## 🧾 Health Check Log Format

Each log includes:

```json
[
  {
    "InstanceId": "i-0cdba79e5a259a4f5",
    "State": "terminated",
    "StatusCheck": "not-applicable",
    "Timestamp": "2025-05-07T06:39:26.932543Z"
  },
  {
    "InstanceId": "i-0deb10f4e39c0d6c5",
    "State": "running",
    "StatusCheck": "ok",
    "Timestamp": "2025-05-07T06:39:26.932543Z"
  },
  {
    "InstanceId": "i-0cb444586fe44a7ef",
    "State": "stopped",
    "StatusCheck": "not-applicable",
    "Timestamp": "2025-05-07T06:39:26.932543Z"
  }
]

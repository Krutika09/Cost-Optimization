# Automating EBS Snapshot Cleanup with AWS Lambda for Cost Efficiency

##  Overview

In AWS, developers often create EC2 instances with attached EBS volumes and take snapshots for backup. However, snapshots can accumulate if not deleted after the associated instance or volume is removed — leading to unnecessary costs.

This project uses **AWS Lambda** to automatically detect and delete unused EBS snapshots. It runs on a schedule using **Amazon CloudWatch (EventBridge)**, enabling a fully serverless and automated cleanup process.

![image](https://github.com/user-attachments/assets/3b4c5a60-ea1a-4fa7-9776-b5459fc7ff54)


##  Services Used

* **AWS Lambda** – Serverless compute for automation logic
* **Amazon EC2** – Virtual machines in the cloud
* **Amazon EBS** – Elastic Block Store volumes
* **Amazon CloudWatch (EventBridge)** – Scheduled or event-driven triggers
* **IAM** – Role-based access and permissions


## 🚀 What is AWS Lambda?

**AWS Lambda** is a serverless compute service. You upload your code, set triggers, and AWS handles the rest — no servers to manage.

### Key Features:

* **Serverless**: No infrastructure to manage
* **Cost-Efficient**: Pay only when your function runs
* **Multi-language support**: Python, Node.js, Java, and more
* **Use Cases**: Automation, event handling, serverless APIs


##  What is EC2 and EBS?

### EC2 (Elastic Compute Cloud):

* Virtual machines in the cloud
* Scalable, secure, and flexible
* Pay-as-you-go pricing

### Common Use Cases:

* Web hosting
* Machine learning
* Testing environments
* High-performance computing

### EBS (Elastic Block Store):

* Storage attached to EC2 instances
* Like a virtual hard drive
* Can be detached/re-attached

### Snapshot:

* Backup of an EBS volume at a specific time
* Useful for disaster recovery and volume cloning
* Stored internally in S3


##  Architecture Overview

**High-Level Flow:**

1. EC2 Instance created with attached EBS Volume
2. Snapshots are taken periodically
3. Instance is terminated, volume and snapshots remain
4. Lambda function identifies unused snapshots
5. Lambda deletes the snapshots


##  Implementation Steps

### 1. Create EC2 instance and attach volume

### 2. Create EBS Snapshots manually or via automation

### 3. Set up AWS Lambda

* **Language**: Python
* **Library**: boto3 (AWS SDK for Python)

![image](https://github.com/user-attachments/assets/aab258a1-5b20-4f8f-81ad-dcd7b78ff9e9)


### 4. Deploy Lambda Function and Set Timeout

* Increase timeout from default (3s) to 10 seconds or more.

![image](https://github.com/user-attachments/assets/0349b16b-c5be-4281-a822-7eac05e082ea)


### 5. Set IAM Permissions

Add the following permissions to your Lambda role:
Create inline policies or attach a custom policy with these permissions.
* `ec2:DescribeSnapshots`
* `ec2:DeleteSnapshot`
* `ec2:DescribeInstances`
* `ec2:DescribeVolumes`

![image](https://github.com/user-attachments/assets/91f16388-2627-4045-9348-7b8378d75a14)



##  Testing

![image](https://github.com/user-attachments/assets/a4643fcf-f24f-4c68-9b5b-1a4b112c26dc)
![image](https://github.com/user-attachments/assets/7f7605a1-83ee-4cb6-ab1f-f3cb9597c1fe)


### Scenario:

* Created EC2 instance → Attached Volume → Took Snapshot
* Terminated EC2 and Volume
* Triggered Lambda manually

### Result:

Lambda detected unused snapshot and deleted it successfully:

```bash
Deleted EBS snapshot snap-0dc087971719a44b9 as its associated volume was not found.
```

![image](https://github.com/user-attachments/assets/df3313b5-2ad0-4eda-9f3a-7c3c76d7b014)

##  Automating Snapshot Deletion Using EventBridge

### Setup:

Instead of scheduled cleanup, you can trigger Lambda immediately upon instance termination.

![image](https://github.com/user-attachments/assets/44603f16-e6cb-4fe6-9935-25c407ec66a0)


### Steps:

1. Go to **Lambda > Add Trigger**
2. Choose **EventBridge (CloudWatch Events)**
3. Create a new rule:

   * Event Source: **AWS Services**
   * Service: **EC2**
   * Event Type: **EC2 Instance State-change Notification**
   * State: **Terminated**

### Result:

When an EC2 instance is terminated, the Lambda function automatically deletes associated volumes and snapshots.

![image](https://github.com/user-attachments/assets/fad4297c-35e1-4ebf-963a-50b1b6a247be)


##  Outcome

By automating snapshot cleanup:

* We **reduce AWS costs** from forgotten resources.
* Ensure a **clean and optimized** cloud environment.
* Eliminate manual cleanup, especially helpful in large environments.


##  Future Improvements

* Add tagging logic to exclude important snapshots from deletion
* Send email notifications for deleted snapshots via Amazon SES
* Use Step Functions for more complex automation chains

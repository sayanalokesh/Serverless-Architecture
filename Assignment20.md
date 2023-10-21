# Graded Assignment on Serverless Architecture

## Table of Contents
1. [EC2 Instance creation](#1-ec2-instance-creation)
2. [IAM Role for Lambda Function](#2-iam-role-for-lambda-function)
3. [Creation of Lambda Function](#3-creation-of-lambda-function)
4. [SNS Creation](#4-sns-creation)
5. [Adding SNS to Lambda as a Trigger](#5-adding-sns-to-lambda-as-a-trigger)
6. [Adding EventBridge (CloudWatch) to Lambda as a Trigger](#6-adding-eventbridge-cloudwatch-to-lambda-as-a-trigger)
7. [Testing the Code](#7-testing-the-code)

---

## Assignment 20: Load Balancer Health Checker

### 1. EC2 Instance creation
- Create 2 EC2 instances with the same security Group and configuration.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/05b8c57d-112c-49e1-8612-55a20f72693a)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/7a72e8de-8150-453b-a7f5-1276df20ec4e)

- Scroll a bit down till you find Target Groups and create a Target Group as shown below.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/3f8e8f5a-2feb-4dda-bed2-56ed881141ab)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/f6493fee-95be-498b-a55b-739ff10cedbb)

- Next, create a Load Balancer by clicking on Application Load Balancer as shown below.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/b0687c1b-dd5a-4f78-8ca3-1f11071304d3)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/2e02acc0-8edb-4b57-b503-45a39abdebe0)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/39e531a7-fae4-4bcc-bc8c-330c3af06ba5)

- Leave all the other options as it is and click on create Load Balancer.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/3508ff0a-c794-468c-9106-4e2d3c179bd9)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/76deb4ea-2f9e-4980-83d1-2977ff4e1cd1)


### 2. IAM Role for Lambda Function
- We need to create a role for the Lambda function with all necessary permissions like EC2, S3, ELB, and SNS. The permissions and steps are as follows.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/ac9b5396-5dad-4786-8c5b-dcedd9b98984)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/3d99bc54-c63d-47e4-82a7-4fc2bc4ba007)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/0e2474ef-4fbe-424d-afdb-4d842bb3f038)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/9a582de8-4a75-4fd2-9b9b-be760096f230)

### 3. Creation of Lambda Function
- We need to create a Lambda function with an IAM role which was created earlier. The steps are as follows.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/ff28b306-6358-4fc8-8238-03d7ebc67382)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/aa27d281-9910-4f70-8acd-5665acdb6096)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/0589c522-7d7e-47b9-9f7e-9111c1b11ae6)


### 4. SNS Creation
- Navigate to SNS from the console and click on Create Topic. Add the details and click on Create Topic.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/e8dcee4d-f07a-4ad1-8ffd-173ab960c71f)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/aba7edf6-e628-4f08-8971-02917450ba01)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/fb47a040-0062-4050-b268-8fa1a4a983d8)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/5802427a-f004-4e18-846b-346966527773)


### 5. Adding SNS to Lambda as a Trigger
- Need to click on Add Trigger and select SNS from the drop-down.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/1d804657-2e1d-44fd-a901-a5285f242915)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/15955cc9-f0d3-4ecb-bdea-81bf877a54ba)

- Once you add the SNS, you will be able to see the same under configuration Triggers.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/5c8a8adb-ed28-48b4-a05e-663847011caa)


### 6. Adding EventBridge (CloudWatch) to Lambda as a Trigger
- Set up a CloudWatch event to trigger this Lambda function every 10 minutes.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/6cf12620-380d-48aa-ab35-7dda1e450f33)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/15061f19-a167-412c-adb1-7039238fc77c)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/42215fab-adfc-41d7-9287-25ad22fd59eb)


### 7. Testing the Code
- Below is the boto3 code.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/5e3026cd-4fcb-4c43-98a7-68bff045ce51)
 ```
import boto3

# Define the ELB name and SNS topic ARN
elb_name = "arn:aws:elasticloadbalancing:ap-south-1:295397358894:targetgroup/LokeshALBQ20/66465dff3538954"  # Replace with your ELB ARN
sns_topic_arn = "arn:aws:sns:ap-south-1:295397358694:LokeshLoadBalancerHealthChecker"  # Replace with your SNS topic ARN

# Initialize the ELB and SNS clients
elb_client = boto3.client('elbv2')
sns_client = boto3.client('sns')

def lambda_handler(event, context):
    # Describe the target health of registered instances
    response = elb_client.describe_target_health(TargetGroupArn=elb_name)

    # Check if any instances are unhealthy
    unhealthy_instances = [instance for instance in response['TargetHealthDescriptions'] if instance['TargetHealth']['State'] != 'healthy']

    if unhealthy_instances:
        # Build a detailed message about unhealthy instances
        message = "Unhealthy instances found in ELB:\n"
        for instance in unhealthy_instances:
            message += f"Instance ID: {instance['Target']['Id']}\n"
            message += f"Description: {instance['TargetHealth']['Description']}\n"
        
        # Publish the message to the SNS topic
        sns_client.publish(
            TopicArn=sns_topic_arn,
            Message=message,
            Subject="ELB Unhealthy Instances Alert"
        )
 ```
- Successfully deployed the code and manually invoked without setting a 10-minute timer.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/bf12348c-83d5-4afa-b856-934a069018be)

- I’ve configured to trigger the EventBridge for 10 minutes and it is working well.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/7b2adedf-99da-4aa6-9ec3-07a57549baa9)

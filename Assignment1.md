# Graded Assignment on Serverless Architecture

## Table of Contents
1. [Assignment 1: Automated Instance Management Using AWS Lambda and Boto3](#assignment-1-automated-instance-management-using-aws-lambda-and-boto3)
    1. [Setup](#setup)
    2. [Lambda Function Creation](#lambda-function-creation)
        1. [Setting up an IAM role](#setting-up-an-iam-role)
        2. [Setting up a Lambda function](#setting-up-a-lambda-function)
    3. [Coding](#coding)
    4. [Testing](#testing)

## Assignment 1: Automated Instance Management Using AWS Lambda and Boto3

### 1. Setup

a. We need to create 2 instances as shown below and name the tags under actions as Auto-Start and Auto-Stop respectively.
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/b6f53fd3-e757-4cb5-8548-d91562207b29)
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/c3ead5bf-3985-4d58-aae7-e45b7e9f925f)
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/94b6b076-3bba-47b1-be7c-3e0bed158209)


### 2. Lambda Function Creation

#### Setting up an IAM role

a. We need to create an IAM role first before creating a Lambda function. Please follow the steps I've included below to create an IAM role for the Lambda function.

b. We need to go to the AWS console and select IAM. We can find Roles under Access Management and click on Create Role.

c. ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/e5f7416e-3798-4c6a-872e-7e35645edfe4)

d. Select Lambda from the services and click next.
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/9c70d4ea-28ba-4c09-a8e4-2ed368f45766)

e. Type EC2full in the search bar and select AmazonEC2FullAccess then click next.
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/351f26ba-c4ce-4754-bc86-10c5e9a9233e)

f. Give a name for the Role and scroll down and click on Create Role.

g. ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/37f8e8a9-7292-49d5-ac06-f0e798b63c5a)
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/c74b37af-2d6a-4a04-83ca-fdb8b423bfbb)

h. A role will be created and you can see the same in the dashboard.
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/b8cccafa-e87b-4b69-9089-0d76a3904caa)


#### Setting up a Lambda function

a. We need to create a Lambda function using the IAM role to execute the task.

b. ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/c20147db-4939-4ce8-b506-bab123dbabcd)

c. Once we select the execution role, we need to click on the create function.
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/1620808d-f98b-40b7-a661-6ba66204340d)

d. Once the function is created, it will appear as below.
![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/ee6a2279-d979-4446-853e-8ec87e94b977)

### 3. Coding

a. Once it is done, we need to scroll down and write a code to finish the task. This code will check the tags we assign at the very beginning. Once the code identifies the tags, it will stop the instance if the tag is identified as Auto-Stop and start the instance if it finds it as Auto-Start.

b. ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/55924ca8-6c38-4dd5-9f08-d56951841ff8)


### 4. Testing

c. The test results show that the code worked fine and stopped the instance with the tag Auto-Stop and started the instance with the tag Auto-Start.
```
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    
    # Stop EC2 instances with the "Action" tag set to "Auto-Stop"
    stop_instances = ec2.describe_instances(Filters=[{'Name': 'tag:Action', 'Values': ['Auto-Stop']}])
    for reservation in stop_instances['Reservations']:
        for instance in reservation['Instances']:
            instance_id = instance['InstanceId']
            ec2.stop_instances(InstanceIds=[instance_id])
            print(f'Stopping instance: {instance_id}')
    
    # Start EC2 instances with the "Action" tag set to "Auto-Start"
    start_instances = ec2.describe_instances(Filters=[{'Name': 'tag:Action', 'Values': ['Auto-Start']}])
    for reservation in start_instances['Reservations']:
        for instance in reservation['Instances']:
            instance_id = instance['InstanceId']
            ec2.start_instances(InstanceIds=[instance_id])
            print(f'Starting instance: {instance_id}')

    return {
        'statusCode': 200,
        'body': 'EC2 instances updated successfully!'
    }

```

d. ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/8207f8a5-e8d2-40a2-9b66-3eec85c17236)

e. ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/cf95b18d-33cc-4029-8fa8-4e1303de6c90)


f. ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/320acc74-48dd-4688-809f-817c391751e6)



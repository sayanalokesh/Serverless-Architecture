# Graded Assignment on Serverless Architecture

## Table of Contents
1. [SNS Setup](#1-sns-setup)
2. [Lambda IAM Role](#2-lambda-iam-role)
3. [Lambda Function](#3-lambda-function)
4. [Testing](#4-testing)

## Assignment 15: Monitor EC2 Instance State Changes Using AWS Lambda, Boto3, and SNS
### Objective
Automatically monitor changes in EC2 instance states and send notifications whenever an instance is started or stopped.

### Task
Set up a Lambda function that listens to EC2 state change events and sends SNS notifications detailing the state changes.

## 1. SNS Setup
- Navigate to the SNS dashboard and create a new topic.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/471dcc86-ace2-475f-9670-4a4927fce327)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/62f5015e-0207-4490-9fc0-14526bf90bcb)

- Subscribe to this topic with your email.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/a0a28608-716f-4515-b1f1-e4cf0ce4dc53)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/14d2ec62-3882-44c8-9c07-d1833417dcfc)

- Once you create a Subscription, you will receive an email stating to confirm the subscription. You will find that email in your spam/inbox.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/8580bbf1-5f3b-440f-a1ac-738c5dc15f6d)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/5d7f877a-dcc3-4de9-932f-d23f658b9381)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/d9b9018d-4c55-4999-a8c9-d303ca6ed7c3)


## 2. Lambda IAM Role
- Create a role with permissions to read EC2 instance states and send SNS notifications.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/f654d482-bf0a-4b9e-b9db-e571a7bd4614)

- Give the permissions to SNS and EC2.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/5d22623a-d68a-4a39-8a8d-92a86de702ab)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/777fec8c-8f4c-4c03-bbee-d87befcf9d21)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/417b92eb-c6bf-4051-8809-47511f5c40ab)

## 3. Lambda Function
- Create a function and assign the above IAM role.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/f9528a3e-a86f-4038-bd74-4a8c5583699a)

- Select the above role and attach it to the Lambda function.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/0cc4968b-4d12-48b9-9374-781831ee6ed2)

- A Lambda function has been created, and you can find the same in the below screenshot.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/a7194113-0712-4e7a-b0f8-962914332d46)

- We have created a function, now we need to configure the Events. The detailed steps have been shown in the below screenshots.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/e7afeb46-4404-4227-a341-e744451f1a99)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/050f091e-0203-4deb-9ccf-f7cb66e17c0f)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/d1e92464-c8e2-47f6-8764-900354ed5fe7)

- Now we need to configure the destination, and steps are shown in the below screenshots.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/cc950507-9daf-4b1a-8a2d-9dddb41b3306)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/e6c86bb4-a2c2-41bf-a8bd-2221311ff9f5)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/d395b35a-f319-438c-a0a0-d19faf39bb57)

- Once we configure, the results will be shown in the below screenshot.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/dc16517a-a9e6-49da-887c-6fd0346d0ec8)

## 4. Testing
- I have written the code and I manually started the Instances.
```
import boto3
import json

def lambda_handler(event, context):
    sns_topic_arn = "arn:aws:sns:us-east-1:297732540443:LokeshMonitorEC2InstanceState"  # Replace with your SNS topic ARN
    ec2 = boto3.client('ec2')
    sns = boto3.client('sns')

    if 'Records' in event:
        for record in event['Records']:
            message = json.loads(record['Sns']['Message'])
            instance_id = message['detail']['instance-id']
            state = message['detail']['state']

            # Describe the instance to get more details
            instance_info = ec2.describe_instances(InstanceIds=[instance_id])
            instance_name = 'N/A'
            
            for tag in instance_info['Reservations'][0]['Instances'][0]['Tags']:
                if tag['Key'] == 'Name':
                    instance_name = tag['Value']

            notification_message = f"EC2 instance {instance_name} ({instance_id}) has changed state to {state}"
            sns.publish(TopicArn=sns_topic_arn, Message=notification_message, Subject="EC2 State Change Notification")

    else:
        # This branch handles cases when the Lambda function is manually invoked without an event
        print("Lambda function was invoked manually without a valid event.")

```
> ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/91641194-4dce-4455-84f4-149490079c85)
> ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/89c63258-69cb-4ecd-b60f-feb407428bab)
> ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/05f6837a-3fc8-4a4f-a0c9-35d18b9592ac)
> ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/2ef12f7c-76f5-4469-8268-020b73383ace)

- As soon as I started the Instance, SNS triggered the Lambda and sent the email.
> ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/ff472f28-fa09-4456-9dee-ea36a1b1aa95)

- I have stopped the Instance, and SNS sent an email regarding the status.
> ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/5359f7e5-3aab-4216-bb10-cca3ff7e3070)

# Graded Assignment on Serverless Architecture

## Table of Contents
1. [S3 Setup](#1-s3-setup)
2. [Lambda IAM Role](#2-lambda-iam-role)
3. [Lambda Function](#3-lambda-function)
4. [Manual Invocation](#4-manual-invocation)

## Assignment 2: Automated S3 Bucket Cleanup Using AWS Lambda and Boto3
### Objective
To gain experience with AWS Lambda and Boto3 by creating a Lambda function that will automatically clean up old files in an S3 bucket.

### Task
Automate the deletion of files older than 30 days in a specific S3 bucket.

## 1. S3 Setup
- Navigate to the S3 dashboard and create a new bucket.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/08000df1-ed7c-4355-bb0f-75be6218c1cb)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/3f0b24f4-4e93-479b-b6b0-f5f31362051d)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/4d2fdec7-1ac3-48f6-bab5-1d38a1d6a6aa)
  
- We have successfully created an S3 bucket with the name "lokeshs3bucketcleanup."
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/b9c552e1-979f-4bdd-b4e3-9b0d433e076e)
  
- We need to upload some random files to test. The command to upload a file without changing the system time is as follows:
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/b11bd0a1-9bf5-425a-ae5b-2eacf3cb9081)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/5bf49c52-a164-409f-8245-3e66e582bed8)

## 2. Lambda IAM Role
- In the IAM dashboard, create a new role for Lambda and select "AmazonS3FullAccess" for the permissions. Then, on the next page, give the name and click on "Create Role."
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/f49fa34d-1195-4779-a343-747b25a8925f)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/1a1eeb74-a197-47e4-b7ef-302a4f44c226)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/610a83bb-0d4b-4e48-b427-458ca0d95ff8)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/35be93a2-a02a-4957-83d3-44970ddf270d)




- A new Role with the name "lokeshs3bucketcleanup" has been created.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/1f60e7df-3700-460c-bc1d-c82fa76179d1)


## 3. Lambda Function
- Navigate to the Lambda dashboard and create a new function with the name "lokeshS3BucketCleanup."
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/bec14b42-a125-49cf-a133-7cdddcb64639)


- In the permissions, select the IAM role we have created above and click on "Create Function." A new function will be created as shown below.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/83a11a50-8537-40c7-af45-57fe606576b5)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/da8cd302-b363-461d-a9ed-4e2fa6b4aba4)

- Since we have created a function, now it's time to write code to execute the task. The code is as follows:

```
  # python
  import boto3
  import datetime

  def lambda_handler(event, context):
      s3 = boto3.client('s3')
      
      # Get the current date
      current_date = datetime.datetime.now()
      
      # Calculate the date 30 days ago
      cutoff_date = current_date - datetime.timedelta(days=30)
      
      objects = s3.list_objects(Bucket='lokeshs3bucketcleanup')
      
      if 'Contents' in objects:
          for obj in objects['Contents']:
              object_key = obj['Key']
              
              # Get object metadata tags
              metadata = s3.head_object(Bucket='lokeshs3bucketcleanup', Key=object_key)
              metadata_tags = metadata.get('Metadata', {})
              
              if 'last-modified' in metadata_tags:
                  last_modified_date = datetime.datetime.strptime(metadata_tags['last-modified'], '%Y-%m-%d')
                  
                  # Check if the tag value indicates a date older than 30 days
                  if last_modified_date < cutoff_date:
                      # Delete the object
                      s3.delete_object(Bucket='lokeshs3bucketcleanup', Key=object_key)
                      print(f"Deleted object: {object_key}")
              
              if metadata_tags:
                  print("Metadata Tags:")
                  for key, value in metadata_tags.items():
                      print(f"  {key}: {value}")
```
> ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/bbf4951e-a0f9-4457-b55d-0060b2db4cbb)

## 4. Manual Invocation
- We have 2 files in the S3 bucket, and now we will test the code to delete the files in the S3 bucket.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/a970b114-b285-4e53-a4b9-345cf8f794ef)
- The screenshot below shows the result, and the files in the S3 bucket have been deleted successfully.
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/0a4efe5a-3495-4147-9f0e-7fdd3215d41a)
  > ![image](https://github.com/sayanalokesh/Serverless-Architecture/assets/105637305/48963593-3c25-4b30-afec-d03d56044b92)

# AWS-Lambda-Trigger_Automation
Setup S3 Trigger with Lambda and dynamoDB. Here, we will update the dynamo DB by fetching/triggering the new files added in the S3 bucket using the AWS lambda function which will we be complete automation.


**Step-1:** <br/>
Create an IAM role (having specific permissions to access the AWS services). <br/>
Go to Roles > create role > AWS services > lambda and select the policy. <br/>
Here policy, we are attaching to our role is “AmazonDynamoDBFullAccess” and move a step ahead to complete further configurations. <br/>
Add a role name, role name can be any, and click on create. <br/>
Follow the below output. <br/>

![1](https://user-images.githubusercontent.com/46487696/118350848-76460200-b576-11eb-8ec1-7f801322b3c2.png)
![2](https://user-images.githubusercontent.com/46487696/118350850-780fc580-b576-11eb-9c2b-d1204a4d6d23.png)
![3](https://user-images.githubusercontent.com/46487696/118350851-780fc580-b576-11eb-8916-22d614ffe8a7.png)
![4](https://user-images.githubusercontent.com/46487696/118350852-78a85c00-b576-11eb-84f5-27aa27df6c69.png)
![5](https://user-images.githubusercontent.com/46487696/118350854-78a85c00-b576-11eb-9273-f45fba937fc7.png)


**Step-2:** <br/>
Create a Lambda function (this function will be used further to trigger the S3 bucket services). <br/>
Here, we will integrate dynamo DB using the python 3.6 version. <br/>
The role must be selected which was created in Step-1. <br/>
![image](https://user-images.githubusercontent.com/46487696/118350956-db015c80-b576-11eb-915b-d909e262ce37.png)
![image](https://user-images.githubusercontent.com/46487696/118350957-ddfc4d00-b576-11eb-94f9-41a28763497e.png)
![image](https://user-images.githubusercontent.com/46487696/118350958-dfc61080-b576-11eb-91e0-653419fdb971.png)

The name of lambda Function can be anything. <br/>
![image](https://user-images.githubusercontent.com/46487696/118350979-0126fc80-b577-11eb-948f-904e1337affa.png)

Here in configuration, you can see the permissions to the services accessed by the function. <br/>
![image](https://user-images.githubusercontent.com/46487696/118350971-f8362b00-b576-11eb-8302-52decbe24bde.png)

Inside the code source, a Python script is implemented to interact with Dynamo DB. <br/>
![image](https://user-images.githubusercontent.com/46487696/118350973-fb311b80-b576-11eb-9e8f-2094ccf377f2.png)

```
import boto3
from uuid import uuid4
def lambda_handler(event, context):
    s3 = boto3.client("s3")
    dynamodb = boto3.resource('dynamodb')
    for record in event['Records']:
        bucket_name = record['s3']['bucket']['name']
        object_key = record['s3']['object']['key']
        size = record['s3']['object'].get('size', -1)
        event_name = record ['eventName']
        event_time = record['eventTime']
        dynamoTable = dynamodb.Table('newtable')
        dynamoTable.put_item(
            Item={'unique': str(uuid4()), 'Bucket': bucket_name, 'Object': object_key,'Size': size, 'Event': event_name, 'EventTime': event_time})
```

**Step-3:** <br/>
Create an S3 bucket to trigger from Lambda function. <br/>
The name of bucket and region can be any for now.<br/>
Allow all the public access as this is the learning phase.<br/>
Finally, the bucket is created. <br/>

![image](https://user-images.githubusercontent.com/46487696/118351043-41867a80-b577-11eb-8ff3-c96112ddc3b3.png)

![image](https://user-images.githubusercontent.com/46487696/118351046-44816b00-b577-11eb-83d2-8e46e5a8f3c2.png)
![image](https://user-images.githubusercontent.com/46487696/118351047-4814f200-b577-11eb-968f-84289e6ebdff.png)

**Step-4:**  <br/>
Now we will set the S3 bucket to trigger it by using the lambda function.<br/>
Go to the “add trigger” and add the S3 bucket which we created earlier.<br/>
Event type is basically a type of functionality we want like put delete and so on. Here we’ll select “All objects create events”. <br/>
Prefix and suffix are used if we want to add a specific file of any extension. <br/>
S3 bucket is ready to trigger using Lambda function. <br/>

![image](https://user-images.githubusercontent.com/46487696/118351111-99bd7c80-b577-11eb-85d6-66b6f7531bd8.png)
![image](https://user-images.githubusercontent.com/46487696/118351112-9cb86d00-b577-11eb-81ce-be7a9189424d.png)
![image](https://user-images.githubusercontent.com/46487696/118351116-9f1ac700-b577-11eb-8002-dde02bdbef81.png)
![image](https://user-images.githubusercontent.com/46487696/118351122-a510a800-b577-11eb-896b-34b2953afcb7.png)


**Step-5:** <br/>
DynamoDB will be used to store the input and outputs of the data from the S3 bucket. <br/>
Go to databases services and select Dynamo DB. <br/>
Here we will take the name of the table i.e “newtable” and partition key “unique”. This value can be changed inside your code we used to integrate the dynamo DB. <br/>
At first, you will see dynamo DB items list are empty.  <br/>

![image](https://user-images.githubusercontent.com/46487696/118351137-c6719400-b577-11eb-83c0-17f88636a325.png)
![image](https://user-images.githubusercontent.com/46487696/118351140-c96c8480-b577-11eb-86af-24e52b68214c.png)

**Step-6:** <br/>

Go to the S3 bucket try adding the file/folder inside your bucket manually, we will see lambda function triggers and update the dynamo DB database. <br/>

First, Below output shows File has been updated in S3 bucket .<br/>

![image](https://user-images.githubusercontent.com/46487696/118351176-02a4f480-b578-11eb-9e9a-b7b81962f54e.png)

Second, DynamoDB in updated using aws lambda by trigerring S3 bucket.<br/>

![image](https://user-images.githubusercontent.com/46487696/118351197-25370d80-b578-11eb-81e8-d7f1d7024fbf.png)
![image](https://user-images.githubusercontent.com/46487696/118351200-27996780-b578-11eb-9d91-5aa8e418f0be.png)

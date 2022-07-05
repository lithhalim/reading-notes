# Introducing Amazon S3 Object Lambda – Use Your Code to Process Data as It Is Being Retrieved from S3

### When you store data in Amazon Simple Storage Service (Amazon S3), you can easily share it for use by multiple applications. However, each application has its own requirements and may need a different view of the data. For example, a dataset created by an e-commerce application may include personally identifiable information (PII) that is not needed when the same data is processed for analytics and should be redacted. On the other side, if the same dataset is used for a marketing campaign, you may need to enrich the data with additional details, such as information from the customer loyalty database.

### To provide different views of data to multiple applications, there are currently two options. You either create, store, and maintain additional derivative copies of the data, so that each application has its own custom dataset, or you build and manage infrastructure as a proxy layer in front of S3 to intercept and process data as it is requested. Both options add complexity and costs, so the S3 team decided to build a better solution.

Today, I’m very happy to announce the availability of S3 Object Lambda, a new capability that allows you to add your own code to process data retrieved from S3 before returning it to an application. S3 Object Lambda works with your existing applications and uses AWS Lambda functions to automatically process and transform your data as it is being retrieved from S3. The Lambda function is invoked inline with a standard S3 GET request, so you don’t need to change your application code.

In this way, you can easily present multiple views from the same dataset, and you can update the Lambda functions to modify these views at any time.

Architecture diagram.

# There are many use cases that can be simplified by this approach, for example:

Redacting personally identifiable information for analytics or non-production environments.
Converting across data formats, such as converting XML to JSON.
Augmenting data with information from other services or databases.
Compressing or decompressing files as they are being downloaded.
Resizing and watermarking images on the fly using caller-specific details, such as the user who requested the object.
Implementing custom authorization rules to access data.
You can start using S3 Object Lambda with a few simple steps:

## Create a Lambda Function to transform data for your use case.
Create an S3 Object Lambda Access Point from the S3 Management Console.
Select the Lambda function that you created above.
Provide a supporting S3 Access Point to give S3 Object Lambda access to the original object.
Update your application configuration to use the new S3 Object Lambda Access Point to retrieve data from S3.
To get a better understanding of how S3 Object Lambda works, let’s put it in practice.

How to Create a Lambda Function for S3 Object Lambda
To create the function, I start by looking at the syntax of the input event the Lambda function receives from S3 Object Lambda:
```javascript
{
    "xAmzRequestId": "1a5ed718-5f53-471d-b6fe-5cf62d88d02a",
    "getObjectContext": {
        "inputS3Url": "https://myap-123412341234.s3-accesspoint.us-east-1.amazonaws.com/s3.txt?X-Amz-Security-Token=...",
        "outputRoute": "io-iad-cell001",
        "outputToken": "..."
    },
    "configuration": {
        "accessPointArn": "arn:aws:s3-object-lambda:us-east-1:123412341234:accesspoint/myolap",
        "supportingAccessPointArn": "arn:aws:s3:us-east-1:123412341234:accesspoint/myap",
        "payload": "test"
    },
    "userRequest": {
        "url": "/s3.txt",
        "headers": {
            "Host": "myolap-123412341234.s3-object-lambda.us-east-1.amazonaws.com",
            "Accept-Encoding": "identity",
            "X-Amz-Content-SHA256": "e3b0c44297fc1c149afbf4c8995fb92427ae41e4649b934ca495991b7852b855"
        }
    },
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "...",
        "arn": "arn:aws:iam::123412341234:user/myuser",
        "accountId": "123412341234",
        "accessKeyId": "..."
    },
    "protocolVersion": "1.00"
}
JSON
```
# The getObjectContext property contains some of the most useful information for the Lambda function:

The inputS3Url is a presigned URL that the function can use to download the original object from the supporting Access Point. In this way, the Lambda function doesn’t need to have S3 read permissions to retrieve the original object and can only access the object processed by each invocation.
The outputRoute and the outputToken are two parameters that are used to send back the modified object using the new WriteGetObjectResponse API.
The configuration property contains the Amazon Resource Name (ARN) of the Object Lambda Access Point and of the supporting Access Point.

The userRequest property gives more information of the original request, such as the path in the URL, and the HTTP headers.

Finally, the userIdentity section returns the details of who made the original request and can be used to customize access to the data.

Now that I know the syntax of the event, I can create the Lambda function. To keep things simple, here’s a function written in Python that changes all text in the original object to uppercase. When creating this function in the Lambda console, I select the Python 3.7 runtime.

import boto3
import requests

```Python
def lambda_handler(event, context):
    print(event)

    object_get_context = event["getObjectContext"]
    request_route = object_get_context["outputRoute"]
    request_token = object_get_context["outputToken"]
    s3_url = object_get_context["inputS3Url"]

    # Get object from S3
    response = requests.get(s3_url)
    original_object = response.content.decode('utf-8')

    # Transform object
    transformed_object = original_object.upper()

    # Write object back to S3 Object Lambda
    s3 = boto3.client('s3')
    s3.write_get_object_response(
        Body=transformed_object,
        RequestRoute=request_route,
        RequestToken=request_token)

    return {'status_code': 200}
Python
Looking at the code of the function, there are three main sections:
```
First, I use the inputS3Url property of the input event to download the original object. Since the value is a presigned URL, the function doesn’t need permissions to read from S3.
Then, I transform the text to be all uppercase. To customize the behavior of the function for your use case, this is the part you need to change. For example, to detect and redact personally identifiable information (PII), I can use Amazon Comprehend to locate PII entities with the DetectPiiEntities API and replace them with asterisks or a description of the redacted entity type.
Finally, I use the new WriteGetObjectResponse API to send the result of the transformation back to S3 Object Lambda. In this way, the transformed object can be much larger than the maximum size of the response returned by a Lambda function. For larger objects, the WriteGetObjectResponse API supports chunked transfer encoding to implement a streaming data transfer. The Lambda function only needs to return the status code (200 OK in this case), eventual errors, and optionally customize the metadata of the returned object as described in the S3 GetObject API.
I package the function and its dependencies, including an updated version of the AWS SDK for Python (boto3) implementing the new write_get_object_response method, and upload it to Lambda. Note that the maximum duration for a Lambda function used by S3 Object Lambda is 60 seconds, and that the Lambda function needs AWS Identity and Access Management (IAM) permissions to call the WriteGetObjectResponse API.

How to Create an S3 Object Lambda Access Point from the Console
In the S3 console, I create an S3 Access Point on one of my S3 buckets:

S3 console screenshot.

Then, I create an S3 Object Lambda Access Point using the supporting Access Point I just created. The Lambda function is going to use the supporting Access Point to download the original objects.

S3 console screenshot.

During the configuration of the S3 Object Lambda Access Point as shown below, I select the latest version of the Lambda function I created above. Optionally, I can enable support for requests using a byte range, or using part numbers. For now, I leave them disabled. To understand how to use byte range and part numbers with S3 Object Lambda, please see the documentation.

S3 console screenshot.


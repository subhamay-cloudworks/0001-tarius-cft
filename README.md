# Project Tarius: AWS Serverlsss Real Time Data Load to DynamoDB

The user / producer uploads a csv source file to the landing zone S3 bucket. A Lambda function is triggered using S3 event notification and loads it to a DynamoDB table. The entire stack is created using CFT (CloudFormation template). The SNS, S3 Bucket and DynamoDB tables are encrypted using Customer Managed KMS Key.

## Description

This sample project demonstrate the capability of loading a .csv file into a DynamoDB table using a Lambda function. The Lambda function is triggered using an Event Source Notification created on the S3 bucket. Once the data loads successfully into the table a SNS notification is published and end users are notified via email subscriibed to the SNS Topic. CloudWatch Alarms are created to demonstrate the different metrics on the Lambda function. The S3 Bucket, SNS Topic and the DynamoDB tables are encrypted using Customer Managed KMS Key. The entire stack (excluding the KMS Key) is created using CloudFormation templates.

![Project Tauris - Design Diagram](https://subhamay-projects-repository-us-east-1.s3.amazonaws.com/0001-tarius/tarius-architecture-diagram.png)

![Project Tauris - Services Used](https://subhamay-projects-repository-us-east-1.s3.amazonaws.com/0001-tarius/tarius-services-used.png)

## Getting Started

### Dependencies

* Create a Customer Managed KMS Key in the region where you want to create the stack..
* Modify the KMS Key Policy to let the IAM user encrypt / decrypt using any resource using the created KMS Key.

### Installing

* Clone the repository.
* Create a S3 bucket and make it public.
* Create the folders - tarius/cft/nested-stacks, tarius/cft/cross-stacks, tarius/code
* Upload the following YAML templates to tarius/cft/nested-stacks
    * cloudwatch-stack.yaml
    * dynamodb-stack.yaml
    * iam-role-stack.yaml
    * s3-stack.yaml
    * sns-stack.yaml
    * sqs-stack
* Upload the following YAML templates to tarius/cft/cross-stacks
    * custom-resource-lambda-stack.yaml
* Upload the following YAML templates to tarius/cft/
    * tarius-root-stack.yaml
* Zip and Upload the Python file  to tarius/cft/code
* Create the cross-stack using the template custom-resource-lambda-stack.yaml by using the S3 url and pass the appropriate parameters.
* Create the entire using by using the root stack template custom-resource-lambda-stack.yaml by providing the required parameters and the s3 cross stack name created in the previous step.

### Executing program

* Upload the sample products.csv file to the landing zone folder of the S3 bucket either using S3 console or CLI
* Step-by-step bullets
```
aws s3 cp products.csv <s3 bucket uri>/data/
```
* Alternatively, the data file can be uploaded to the S3 bucket from AWS Console as well and verify the data in the DynamoDB table.

* To test the SQS Dead Letter Queue, put an exception in the handler after the first logger statement as   
    
```
    now = datetime.now()
    current_time = now.strftime("%H:%M:%S")
    raise Exception(f"{current_time} :: Something bad happened.") 
```
and asynchronously invoke the lambda using the follwing command:
```
aws lambda invoke --invocation-type Event --function-name tarius-lambda-devl-us-east-1 --region us-east-1 --payload '{ "key1": "value1","key2":"value2" }' --cli-binary-format raw-in-base64-out response.json --profile default
```

After 2 failed attempts if the lambda fails for the third time, then the event is pushed into the dead letter queue.
## Help

Post message in my blog (https://blog.subhamay.com)


## Authors

Contributors names and contact info

Subhamay Bhattacharyya  - [subhamoyb@yahoo.com](https://subhamay.blog)

## Version History

* 0.1
    * Initial Release
* 0.2
    * Fixed the Python Lambda bug and some typo errors.

## License

This project is licensed under Subhamay Bhattacharyya. All Rights Reserved.

## Acknowledgments

Inspiration, code snippets, etc.
* [Stephane Maarek ](https://www.linkedin.com/in/stephanemaarek/)
* [Neal Davis](https://www.linkedin.com/in/nealkdavis/)
* [Adrian Cantrill](https://www.linkedin.com/in/adriancantrill/)

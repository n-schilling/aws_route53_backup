AWS Route53 backup and restore solution
----

If you are using the AWS service Route53 you should think about creating a backup for your Hosted Zones. If you are using IaC (CloudFormation, Terraform for example) to create Hosted Zones and the record entries, maybe a backup solution is not necessary.

This solution is based on [this work](https://github.com/jacobfgrant/route53-lambda-backup) and just provides a serverless based deployment for it. Including some small code changes.

:exclamation: This solution will create several resources which will be billed by AWS.

## Base architecture

![alt text](/images/aws_route53_backup_architecture.png "Route53 backup architecture")

A Cloudwatch Scheduler starts every six hours an AWS Lambda function. This function is allowed to read all Route 53 resources. It creates a JSON file with all Hosted Zones and Record entries per run. This is saved with a timestamp into an AWS S3 bucket.

To restore a specific configuration you need to configure the timestamp of the S3 file as an environmental variable and start the AWS Lambda restore function manually.

### Used AWS Services

* AWS Route 53 (just as the backup source)
* AWS Simple Storage Service (S3)
* AWS Lambda
* AWS X-AWS
* AWS Cloudwatch

## Implementation guide

Please follow all the steps below to deploy the solution. Please pay attention that the solution only can be deployed in a region where all mentioned services above are available.

## Requirements

* Python 3 (tested with version 3.7.0)
* Node.js (tested with version 10.15.1)
* Serverless (tested with version 1.37.1)
* Serverless Plugins
  * AWS Pseudo Parameters (install via ```npm install serverless-pseudo-parameters```)
  * serverless plugin tracing (install via ```npm install serverless-plugin-tracing```)

## Deploy the solution

1. Clone this repository
2. Edit the parameters in the serverless.yml
  * s3BucketName: The AWS S3 bucket where the backup files are stored
  * backupInterval: Backup interval in full hours (1,2,3...)
3. Deploy the solution to AWS with ```sls deploy --region eu-west-1 --stage prod```.
4. Check the first backup file in the configured S3 bucket to make sure that the backup solution works

### Undeploy the solution

Just follow the next steps to undeploy the solution:

1. Open the AWS Console and clean up the s3 bucket
2. run ```sls remove``` to remove the solution

## ToDo list

### Code

There are surely ways to improve. Just send a pull request. :wink:

### Functionality / Architecture

Priority from high to low:

* Restore functions
* Backup to a different AWS account (always possible, the source account just need access - just more description is needed)

# S3 Bucket Access

### Requirements

* Secure access and secret keys storage
* Separate policies for read and write
* Encryption at rest & in transit
* Logging & monitoring
* Credential rotation

### Solution

The [cloudformation template](./cfn-template.json) creates the following resources 

* Separate groups for read and write permissions
* Users with read and write groups assigned to them
* IAM keys for users are stored in secrets manager

It can take two parameters
* `product` - name of the product; default value is `s3.satie.io`
* `environment` - environment the resources are being created for e.g. dev, test, prod; default value is `dev`

To create the resources, run the `create-stack` command as follows

```shell
$ aws cloudformation create-stack --stack-name atpco-routehappy --capabilities=CAPABILITY_NAMED_IAM --template-body file://cfn-template.json
{
    "StackId": "arn:aws:cloudformation:us-east-1:123456789012:stack/atpco-routehappy/3c2922e0-1131-11ea-bfd4-0e28043c5c55"
}
```

Monitor status of stack creation with the following command

```shell
$ aws cloudformation describe-stacks --stack atpco-routehappy
{
    "Stacks": [
        {
            "StackId": "arn:aws:cloudformation:us-east-1:123456789012:stack/atpco-routehappy/3c2922e0-1131-11ea-bfd4-0e28043c5c55",
            "StackName": "atpco-routehappy",
            "Parameters": [
                {
                    "ParameterKey": "product",
                    "ParameterValue": "s3.satie.io"
                },
                {
                    "ParameterKey": "environment",
                    "ParameterValue": "dev"
                },
                {
                    "ParameterKey": "region",
                    "ParameterValue": "us-east-1"
                }
            ],
            "CreationTime": "2019-11-27T16:16:14.982Z",
            "RollbackConfiguration": {},
            "StackStatus": "CREATE_COMPLETE",
            "DisableRollback": false,
            "NotificationARNs": [],
            "Capabilities": [
                "CAPABILITY_NAMED_IAM"
            ],
            "Outputs": [
                {
                    "OutputKey": "S3ReadWriteGroup",
                    "OutputValue": "atpco-routehappy-s3.satie.io-dev-ReadWrite-Group",
                    "Description": "Read-write managed group for S3 bucket"
                },
                {
                    "OutputKey": "ReadOnlyUserArn",
                    "OutputValue": "arn:aws:iam::123456789012:user/atpco-routehappy-s3.satie.io-dev-ReadOnly",
                    "Description": "Read-only user"
                },
                {
                    "OutputKey": "ReadWriteUserArn",
                    "OutputValue": "arn:aws:iam::123456789012:user/atpco-routehappy-s3.satie.io-dev-ReadWrite",
                    "Description": "Read-write user"
                },
                {
                    "OutputKey": "ReadOnlyUserSecret",
                    "OutputValue": "arn:aws:secretsmanager:us-east-1:123456789012:secret:atpco-routehappy-s3.satie.io-dev-ReadOnly-Secret-vjxJbd",
                    "Description": "Secrets Manager secret with IAM credentials for read-only user"
                },
                {
                    "OutputKey": "BucketName",
                    "OutputValue": "atpco-routehappy-s3.satie.io-dev",
                    "Description": "S3 bucket"
                },
                {
                    "OutputKey": "ReadWriteUserSecret",
                    "OutputValue": "arn:aws:secretsmanager:us-east-1:123456789012:secret:atpco-routehappy-s3.satie.io-dev-ReadWrite-Secret-fzEYjZ",
                    "Description": "Secrets Manager secret with IAM credentials for read-write user"
                },
                {
                    "OutputKey": "S3ReadOnlyGroup",
                    "OutputValue": "atpco-routehappy-s3.satie.io-dev-ReadOnly-Group",
                    "Description": "Read-only group for S3 bucket"
                }
            ],
            "Tags": [],
            "EnableTerminationProtection": false,
            "DriftInformation": {
                "StackDriftStatus": "NOT_CHECKED"
            }
        }
    ]
}
```

To retrieve the IAM keys for read and write users from Secrets Manager, run the following commands

```shell
$ aws secretsmanager get-secret-value --secret-id arn:aws:secretsmanager:us-east-1:123456789012:secret:atpco-routehappy-s3.satie.io-dev-ReadWrite-Secret-fzEYjZ
{
    "ARN": "arn:aws:secretsmanager:us-east-1:123456789012:secret:atpco-routehappy-s3.satie.io-dev-ReadWrite-Secret-fzEYjZ",
    "Name": "atpco-routehappy-s3.satie.io-dev-ReadWrite-Secret",
    "VersionId": "cc75e0d3-bd15-44bd-811c-c9f0bc75e9c3",
    "SecretString": "{\"UserID\": \"arn:aws:iam::123456789012:user/atpco-routehappy-s3.satie.io-dev-ReadOnly\", \"ACCESS_KEY\": \"XXXXWQMWYYYYJMBGZZZZ\", \"SECRET_ACCESS_KEY\": \"5IkSQKX3IKMk1234BbwOzGwe5678gPSKFUloImHq\"}",
    "VersionStages": [
        "AWSCURRENT"
    ],
    "CreatedDate": 1574871487.414
}
```

If you have [`jq`](https://stedolan.github.io/jq/) installed, you can filter on just he the `SecretString`

```shell
$ aws secretsmanager get-secret-value --secret-id arn:aws:secretsmanager:us-east-1:123456789012:secret:atpco-routehappy-s3.satie.io-dev-ReadWrite-Secret-fzEYjZ | jq '.SecretString'
"{\"UserID\": \"arn:aws:iam::123456789012:user/atpco-routehappy-s3.satie.io-dev-ReadOnly\", \"ACCESS_KEY\": \"XXXXWQMWYYYYJMBGZZZZ\", \"SECRET_ACCESS_KEY\": \"5IkSQKX3IKMk1234BbwOzGwe5678gPSKFUloImHq\"}"
```

### TODO
* Credential rotation
* Enable AWS Config rules for S3
* Setup CloudWatch alarms

### References
* [Security best practices for S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/security-best-practices.html)
* [Secure files in S3](https://aws.amazon.com/premiumsupport/knowledge-center/secure-s3-resources/)
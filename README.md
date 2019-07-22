# VPC with private and public subnets in two Availability Zones

This reference architecture provides a set of YAML templates for deploying the following AWS services :
- Amazon IAM
- Amazon S3
- Amazon VPC
- Amazon Ec2
- Amazon ELB

## Prerequisites Notes

Before you can deploy this process, you need the following:
 - Amazon EC2 key pair named belongtest imported into AWS.
    There are some public and private keys that can be used attached with this repo
 - create a publicly accessible s3 folder named "cf-templates-belongtest-paulmateos"  

## Tested on the following Region:
 - ap-southeast-2

This template describes a VPC with a private and public subnets.


## Template details

The templates below are included in this repository and reference architecture:

| Template | Description |
| --- | --- | 
| [master.yaml](master.yaml) | This is the master template - deploy it to CloudFormation and it includes all of the nested templates automatically. |
| [infrastructure/vpc-iam.yaml](infrastructure/vpc-iam.yaml) | This template deploys will create policy to allow EC2 instance full access to S3 & CloudWatch, and VPC Logs to CloudWatch. |
| [infrastructure/vpc-s3bucket.yaml](infrastructure/vpc-s3bucket.yaml) | This template deploys Backup Data Bucket with security data at rest and archive objects greater than 60 days, and ELB logging. |
| [infrastructure/vpc-vpc.yaml](infrastructure/vpc-vpc.yaml) | This template deploys a VPC with a pair of public and private subnets spread across two Availability Zones. It deploys an [Internet gateway](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html), with a default route on the public subnets. It deploys 2 [NAT gateways](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-nat-comparison.html), and default routes for them in the private subnets. |
| [vpc-elb.yaml](vpc-elb.yaml) | This is the load balancer template - deploy it to CloudFormation and it will allow public access to the private subnet and the ec2 instance. |
| [vpc-ec2.yaml](vpc-ec2.yaml) | This is the ec2 template - deploy it to CloudFormation and it will create an ec2 instance in the private subnet. |
After the CloudFormation templates have been deployed, the [stack outputs](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html) contain a link to the site URLs.


## How do I...?

### Get started and deploy this into my AWS account

You can launch this CloudFormation stack in your account:

Example using AWS CLI Command :

1. First Download this code into your workstation, make your own changes and make the prerequisites updates.
 - Your AWS account must have one VPC available to be created in the selected region.
 - Create Amazon EC2 key pair named belongtest

2. Next install [AWS CLI](aws.amazon.com/cli) in your workstation.

3. Upload the files in the "infrastructure" directory into to your own S3 bucket.
 - Eg. aws s3 cp --recursive infrastructure/ s3://cf-templates-belongtest-paulmateos/ (xx being the day of the month)
4. You can run the master.yaml file from your workstation.


```
Example Setup:

Stage1 (~ 10 - 15 minutes)
===========================
To create a environment :
aws cloudformation create-stack \
--stack-name <env> \
--capabilities=CAPABILITY_NAMED_IAM \
--parameters file://parameters.json
--template-body file://master.yaml

To update a environment :
aws cloudformation update-stack \
--stack-name <env> \
--capabilities=CAPABILITY_NAMED_IAM \
--parameters file://parameters.json
--template-body file://master.yaml

To delete a environment :
aws cloudformation delete-stack --stack-name <env>

<env> - Note :stack-name that can be used are (devVPC, stagingVPC, prodVPC)

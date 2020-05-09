# aws-ms-wordpress-ha
Production ready (HA) Wordpress Deployment for AWS using Fargate and Aurora Serverless.

## Architecture
![Architecture](https://www.lucidchart.com/publicSegments/view/460a92be-5c10-49c2-bbae-f2811c081918/image.png)

## Usage

### Pre-Requisites
#### Opt-In For New Resource Id Format
This stack uses tag propagation which requires new [resource-id format](https://aws.amazon.com/blogs/compute/migrating-your-amazon-ecs-deployment-to-the-new-arn-and-resource-id-format-2/)
Before launching cloudformation stack.

#### Register Public Domain
The stack expects a public domain to be registered prior to launching the stack.

_Note: For testing, you may modify the host file and use self signed certificates._

#### Setup DNS
This stack expects Route53 Zone to be pre-created. Either it can be created manually or using cloudformation template.
[![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?templateURL=https://wp-cfn-deploy-s3-cloudformationbucket-1imzg0gegwkod.s3-us-west-2.amazonaws.com/v1/infrastructure/cloudformation/wp-dns.yaml&stackName=wordpress-dns)

Once the zone is created, updated the name server of the domain registrar to point to this zone.

#### ACM Certificates
- Login to AWS Account (dev/production) and go to [ACM wizard](https://console.aws.amazon.com/acm/home?region=us-west-2#/wizard/).
- Switch to desired region where this stack needs to be created.
- Add domain names for loadbalancer: lb.example.com
- Use DNS Validation for verifying domain.
- Create the request and use option : "Create record in route53" to automatically create verification records.
- Now switch region to us-east-1 and create new [ACM Certificate for cloudfront](https://console.aws.amazon.com/acm/home?region=us-east-1#/wizard/)
- Add public facing domains to the certificate: example.com, www.example.com
- Use DNS Validation for verifying domain.
- Create the request and use option : "Create record in route53" to automatically create verification records.


### Launch Stack
Stack                          | Description                                 | Quick Launch Url
-------------------------------|---------------------------------------------|-----------------------------------
wordpress-fullstack            | Launches full wordpress stack with vpc      | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?templateURL=https://wp-cfn-deploy-s3-cloudformationbucket-1imzg0gegwkod.s3-us-west-2.amazonaws.com/v1/infrastructure/cloudformation/wp-fullstack-vpc.yaml&stackName=wordpress)
wordpress-fullstack-no-vpc     | Launches full wordpress stack without vpc   | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?templateURL=https://wp-cfn-deploy-s3-cloudformationbucket-1imzg0gegwkod.s3-us-west-2.amazonaws.com/v1/infrastructure/cloudformation/wp-fullstack-no-vpc.yaml&stackName=wordpress)

*Note: To deploy to different region, simply change region in AWS Console*

## Contributing

### Pre-Requisites
- Docker
- python3
- pip3


### Pre-Commit Hooks
```
pip3 install -r pre-commit
pre-commit install
```

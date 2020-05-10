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

#### Public ACM Certificates
- Login to AWS Account (dev/production) and go to [ACM wizard](https://console.aws.amazon.com/acm/home?region=us-west-2#/wizard/).
- Switch to desired region where this stack needs to be created.
- Add domain names for loadbalancer: lb.example.com
- Use DNS Validation for verifying domain.
- Create the request and use option : "Create record in route53" to automatically create verification records.
- Now switch region to us-east-1 and create new [ACM Certificate for cloudfront](https://console.aws.amazon.com/acm/home?region=us-east-1#/wizard/)
- Add public facing domains to the certificate: example.com, www.example.com
- Use DNS Validation for verifying domain.
- Create the request and use option : "Create record in route53" to automatically create verification records.

#### Nginx TLS Certificates
For end-to-end encryption, we need to create TLS certificates for the application container as well.
The certificate and private key needs to be stored into SSM Parameter as secure string.

- Generate self signed certificates for the container
```bash
mkdir -p certs
openssl req -new -newkey rsa:2048 -days 1825 -nodes -x509 -keyout certs/server.key -out certs/server.crt
```
- Add private key to SSM Parameter store as secure string.
```bash
aws ssm put-parameter \
    --name "/wordpress/ssl/server.key" \
    --type "SecureString" \
    --value "file://./certs/server.key" \
    --description "Wordpress SSL Private Key" \
    --overwrite \
    --tier Intelligent-Tiering \
    --region <region>
```
- Add bundled cert to SSM Parameter store as secure string
```bash
aws ssm put-parameter \
    --name "/wordpress/ssl/server.crt" \
    --type "SecureString" \
    --value "file://./certs/server.crt" \
    --description "Wordpress SSL Cert Bundle" \
    --overwrite \
    --tier Intelligent-Tiering \
    --region <region>
```

#### Client VPN Certificates
This step is only required if using wordpress full stack with VPC or when client vpn stack is setup directly.
The stack uses mutual authentication for setting up client VPN.

Follow the steps at [AWS Client VPN Documentation](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/authentication-authorization.html#mutual)
to create
- CA Certificate
- Server Certificate
- Client Certificate

Only Server certificate needs to be uploaded to ACM.


### Launch Stack
Stack                          | Description                                                | Quick Launch Url
-------------------------------|------------------------------------------------------------|-----------------------------------
wordpress-fullstack            | Launches full wordpress stack with vpc and client vpn      | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?templateURL=https://wp-cfn-deploy-s3-cloudformationbucket-1imzg0gegwkod.s3-us-west-2.amazonaws.com/v1/infrastructure/cloudformation/wp-fullstack-vpc.yaml&stackName=wordpress)
wordpress-fullstack-no-vpc     | Launches full wordpress stack without vpc                  | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?templateURL=https://wp-cfn-deploy-s3-cloudformationbucket-1imzg0gegwkod.s3-us-west-2.amazonaws.com/v1/infrastructure/cloudformation/wp-fullstack-no-vpc.yaml&stackName=wordpress)
vpn-client                     | Client VPN Stack for connecting to admin                   | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?templateURL=https://wp-cfn-deploy-s3-cloudformationbucket-1imzg0gegwkod.s3-us-west-2.amazonaws.com/v1/infrastructure/cloudformation/wp-vpn-client.yaml&stackName=wordpress-client-vpn)


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

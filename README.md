# aws-ms-wordpress-ha
Production ready (HA) Wordpress Deployment for AWS

## Architecture
![Architecture](https://www.lucidchart.com/publicSegments/view/460a92be-5c10-49c2-bbae-f2811c081918/image.png)

## Usage
Stack                    | Description                                 | Quick Launch Url
-------------------------|---------------------------------------------|-----------------------------------
wordpress-fullstack      | Launches full wordpress stack with vpc      | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?templateURL=https://wp-cfn-deploy-s3-cloudformationbucket-1imzg0gegwkod.s3-us-west-2.amazonaws.com/v1/infrastructure/cloudformation/wp-fullstack-vpc.yaml&stackName=wordpress)

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

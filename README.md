## Overview

This is the [AWS Cloudformation](https://aws.amazon.com/cloudformation/) template I used to develop my [wedding website](https://github.com/dylburger/dylbaum.com). If you're trying to create your own website or want to learn more about Cloudformation, I hope this helps.

This guide assumes you have an active AWS account, with permissions to create the necessary resources.

This Cloudformation stack is provided as an example, and creates resources that may not be necessary for your website. **Please examine this template carefully, making modifications as necessary, before deploying it**.

## Infrastructure as code

If you're aware of the benefits of "infrastructure as code", you can skip this section.

If you've ever used AWS before, you've probably created resources using the AWS Console. You might have also interacted with the AWS API via the [AWS Command Line Interface](https://aws.amazon.com/cli/) or [`boto`](https://aws.amazon.com/cli/). This is a fine way to manage your infrastructure, but you might reach the point where you're creating the same set of resource groups over and over again for different projects. Or you might be creating multiple environments for the same resources (maybe `dev` and `prod`).

You could script these operations, making API calls to create what you need, passing in relevant parameters as variables to the script. But as your product grows, this can become unweildy. For instance, some resources depend on others, so you have to manage these dependencies in your script. You may find yourselves repeating common portions of code across scripts. Enter Cloudformation.

Cloudformation allows you to define infrastructure as config files (YAML or JSON). For example, we can create an S3 bucket with a basic website configuration:

    # WebsiteRootDomainBucket is the logical identifier for the resource
    WebsiteRootDomainBucket:
        Type: "AWS::S3::Bucket"
        Properties:
            BucketName: !Ref WebsiteDomain
            WebsiteConfiguration:
                IndexDocument: index.html

This declarative syntax facilitates resource creation. These blocks are more terse and clear than the corresponding AWS CLI or `boto` code. You can manage these templates using version control. With properties on separate lines, you can easily `git diff` changes to infrastructure over time.

Cloudformation manages resource dependencies, creating the right resources in the right order. If one part of the stack (a collection of resources) fails to be created, the whole set of changes is rolled back.

## Usage

Create the Cloudformation stack by running the following command, replacing the ParameterValue entries with values specific to your site.

    aws cloudformation create-change-set --stack-name wedding-website \
        --region <TARGET_AWS_REGION>
        --profile <PROFILE>
        --template-body file://website.template.yaml \
        --change-set-type CREATE \
        --change-set-name creating-stack \
        --parameters ParameterKey=WebsiteDomain,ParameterValue=mysite.com \
            ParameterKey=WebsiteHost,ParameterValue=www.mysite.com \
            ParameterKey=CertificateArn,ParameterValue=<Certificate ARN>

## Notes

Some of the scripts in this repo depend on third party Python libraries (e.g. `boto3` for interacting with AWS). Please create a `python3` [virtual environment](http://docs.python-guide.org/en/latest/dev/virtualenvs/) and install the dependencies contained in `requirements.txt` by running:

    pip install -r requirements.txt

## Usage

Create the Cloudformation stack by running the following command, replacing the ParameterValue entries with values specific to your site.

    aws cloudformation create-change-set --stack-name wedding-website \
        --region <TARGET_AWS_REGION>
        --profile <PROFILE>
        --template-body file://website.template.yaml \
        --change-set-type CREATE \
        --change-set-name creating-stack \
        --parameters ParameterKey=WebsiteDomain,ParameterValue=mysite.com \
            ParameterKey=WebsiteHost,ParameterValue=www.mysite.com

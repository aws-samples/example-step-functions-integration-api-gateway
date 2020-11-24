# Example Step Functions API Gateway integration 

The new Step Functions integration with API Gateway provides an additional resource type, ` arn:aws:states:::apigateway:invoke ` and can be used with both Standard and Express workflows. It allows customers to call API Gateway REST APIs and API Gateway.

![API Gateway Step Fcuntions service integration](/repoResources/stateMachine.png)
 
This project contains source code and supporting files for a serverless application that you can deploy with the SAM CLI. It includes the following files and folders:

- functions - Code for the application's Lambda functions to check the value of, buy, or sell shares of a stock.
- statemachines - Definition for the state machine that orchestrates the stock trading workflow.
- template.yaml - A template that defines the application's AWS resources.

This application creates a mock stock trading workflow which runs on a pre-defined schedule (note that the schedule is disabled by default to avoid incurring charges). It demonstrates the power of Step Functions to orchestrate Lambda functions and other AWS resources to form complex and robust workflows, coupled with event-driven development using Amazon EventBridge.

When the workflow is run, a Lambda function is invoked via a GET request from API Gateway to the /check resource. This returns a random stock value between 1 and 100.  This value is evaluated in the Buy or Sell choice step, depending on if it is less or more than 50. The Sell and Buy states use the API Gateway integration to invoke a Lambda function, with a POST method. A stock_value is provided in the POST request body. A transaction_result is returned in the ResponseBody and provided to the next state. The final state writes a log of the transition to a DynamoDB table.

## Deploy the sample application

The Serverless Application Model Command Line Interface (SAM CLI) is an extension of the AWS CLI that adds functionality for building and testing Lambda applications. It uses Docker to run your functions in an Amazon Linux environment that matches Lambda.

To use the SAM CLI, you need the following tools:

* SAM CLI - [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
* Node.js - [Install Node.js 12](https://nodejs.org/en/), including the NPM package management tool.
* Docker - [Install Docker community edition](https://hub.docker.com/search/?type=edition&offering=community)

To build and deploy your application for the first time, run the following in your shell:

1.	Clone the GitHub repository:
```bash
$ git clone https://github.com/aws-samples/example-step-functions-integration-api-gateway.git
$ cd example-step-functions-integration-api-gateway 
```

2.	Deploy the applicaiton resources:

```bash
sam build
sam deploy --guided
```

The first command will build the source of your application. The second command will package and deploy your application to AWS, with a series of prompts:

* **Stack Name**: The name of the stack to deploy to CloudFormation. This should be unique to your account and region, and a good starting point would be something matching your project name.
* **AWS Region**: The AWS region you want to deploy your app to.
* **Confirm changes before deploy**: If set to yes, any change sets will be shown to you before execution for manual review. If set to no, the AWS SAM CLI will automatically deploy application changes.
* **Allow SAM CLI IAM role creation**: Many AWS SAM templates, including this example, create AWS IAM roles required for the AWS Lambda function(s) included to access AWS services. By default, these are scoped down to minimum required permissions. To deploy an AWS CloudFormation stack which creates or modified IAM roles, the `CAPABILITY_IAM` value for `capabilities` must be provided. If permission isn't provided through this prompt, to deploy this example you must explicitly pass `--capabilities CAPABILITY_IAM` to the `sam deploy` command.
* **Save arguments to samconfig.toml**: If set to yes, your choices will be saved to a configuration file inside the project, so that in the future you can just re-run `sam deploy` without parameters to deploy changes to your application.

You can find your State Machine ARN in the output values displayed after deployment.

## Manually trigger the workflow from a terminal window:

``` bash
aws stepFunctions start-execution \
--state-machine-arn <StockTradingStateMachineArnValue>
```




## Cleanup
```

## Cleanup

To delete the sample application that you created, use the AWS CLI. Assuming you used your project name for the stack name, you can run the following:

```bash
aws cloudformation delete-stack --stack-name sfn-apigw-example2
```

## Resources

See this [AWS compute blog](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) for more information on the Step Functions service integration with API Gateway.

See the [AWS SAM developer guide](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) for an introduction to SAM specification, the SAM CLI, and serverless application concepts.

Next, you can use AWS Serverless Application Repository to deploy ready to use Apps that go beyond hello world samples and learn how authors developed their applications: [AWS Serverless Application Repository main page](https://aws.amazon.com/serverless/serverlessrepo/)

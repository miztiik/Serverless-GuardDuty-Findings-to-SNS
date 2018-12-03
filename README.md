# Amazon GuardDuty Findings to SNS

Every GuardDuty finding is assigned a finding ID. For every finding with a unique finding ID, GuardDuty aggregates all subsequent occurrences of a particular finding that take place in six-hour intervals into a single event. 
GuardDuty then sends a notification about these subsequent occurrences based on this event. We can use this to push the notifications into SNS topic, and getting the security teams to investigate the findings.

![Fig : Amazon GuardDuty Findings to SNS](https://raw.githubusercontent.com/miztiik/Serverless-GuardDuty-Findings-to-SNS/master/images/Serverless-GuardDuty-Findings-To-SNS.png)

This AWS Lambda function will help you to automatically push GuardDuty findings to an SNS topic which can be used by ITSM tools for their workflows.

#### Follow this article in [Youtube](https://www.youtube.com/watch?v=OHXDPDc1qEE&list=PLxzKY3wu0_FKok5gI1v4g4S-g-PLaW9YD&index=20)

## Pre-Requisities
We will need the following pre-requisites to successfully complete this activity,
- A `SNS` topic to which our lambda will publish the GuardDuty Findings. _[Help for setting up SNS Topic](https://www.youtube.com/watch?v=7Ic1SQbjpOs&index=44&list=PLxzKY3wu0_FLaF9Xzpyd9p4zRCikkD9lE)_
  - _`<ARN-OF-YOUR-SNS-TOPIC>`_ - We need this to update in the IAM Policy
  - An email address already subscribed to this topic
- IAM Role - _i.e_ `Lambda Service Role` - _with_ two permissions; _[Help for setting up IAM Role](https://www.youtube.com/watch?v=5g0Cuq-qKA0&list=PLxzKY3wu0_FLaF9Xzpyd9p4zRCikkD9lE&index=11)_
  - `AWSLambdaBasicExecutionRole` - To allow Lambda to log events
  - _`InlinePolicy`_ - To allow Lambda to publish to SNS topic

## Step 0: IAM Policy
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "sns:Publish",
            "Resource": "<ARN-OF-YOUR-SNS-TOPIC>"
        }
    ]
}
```

## Step 1 - Configure Lambda Function- `Serverless Janitor`
The python script is written(_and tested_) in `Python 3.6`. Remember to choose the same in AWS Lambda Functions.

### Customisations
- _Change the global variables at the top of the script to suit your needs._
  - `globalVars['SNSTopicArn']]` - Update the code with your _`<ARN-OF-YOUR-SNS-TOPIC>`_; you can also do that using Lambda `Environment variables`

- `Copy` the code from `Serverless-GuardDuty-Findings-To-SNS` in this repo to the lambda function
  - Consider increasing the lambda run time as needed, the default is `3` seconds.
 - `Save` the lambda function

## Step 2 - Configure Lambda Triggers
Goto the Cloudwatch Dashboard, We are going to use `Event Rules`
1. Choose `Create a new Rule`
1. For `Event Source` - Choose `Event pattern`
   1. For `Service`, Choose/Type `GuardDuty`
   1. For `Event Type`, Choose `GuardDuty Finding`
1. For `Target`, Choose `Lambda Function`
   1. _From dropdown select your Lambda Function Name_
1. In the bottom, `Configure Details`
1. Fill the `Rule Name` & `Rule Description`
   1. _Make sure it is **Enabled**_
1. `Enable` Trigger by `Checking` the box
1. Click `Save`

Now your lambda function should be triggered when ever there is a GuardDuty Findings


## Step 3 - Testing the solution
Goto GuardDuty Dashboard. Here we can generate some sample findings from `Settings` Tab. 

Or if you want more sophisticated testing, try out [this](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html#guardduty_findings-scripts)
If you dont have any, considering trying out my [Serverless AMI Baker](https://github.com/miztiik/Serverless-AMI-Baker/blob/master/README.MD).

### Summary
We have demonstrated how you can automatically push the findings to SNS Topic.
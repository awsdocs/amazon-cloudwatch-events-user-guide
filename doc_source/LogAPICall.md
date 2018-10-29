# Tutorial: Log AWS API Calls Using CloudWatch Events<a name="LogAPICall"></a>

You can use an AWS Lambda function that logs each AWS API call\. For example, you can create a rule to log any operation within Amazon EC2, or you can limit this rule to log only a specific API call\. In this tutorial, you log every time an Amazon EC2 instance is stopped\.

## Prerequisite<a name="log-api-prerequisite"></a>

Before you can match these events, you must use AWS CloudTrail to set up a trail\. If you do not have a trail, complete the following procedure\.

**To create a trail**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Choose **Trails**, **Add new trail**\.

1. For **Trail name**, type a name for the trail\.

1. For **S3 bucket**, type the name for the new bucket to which CloudTrail should deliver logs\.

1. Choose **Create**\.

## Step 1: Create an AWS Lambda Function<a name="api-create-lambda-function"></a>

Create a Lambda function to log the API call events\. Specify this function when you create your rule\.

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. If you are new to Lambda, you see a welcome page\. Choose **Get Started Now**\. Otherwise, choose **Create a Lambda function**\.

1. On the **Select blueprint** page, type `hello` for the filter and choose the **hello\-world** blueprint\.

1. On the **Configure triggers** page, choose **Next**\.

1. On the **Configure function** page, do the following:

   1. Type a name and description for the Lambda function\. For example, name the function "LogEC2StopInstance"\.

   1. Edit the sample code for the Lambda function\. For example:

      ```
      'use strict';
      
      exports.handler = (event, context, callback) => {
          console.log('LogEC2StopInstance');
          console.log('Received event:', JSON.stringify(event, null, 2));
          callback(null, 'Finished');
      };
      ```

   1. For **Role**, choose **Choose an existing role**\. For **Existing role**, select your basic execution role\. Otherwise, create a new basic execution role\.

   1. Choose **Next**\.

1. On the **Review** page, choose **Create function**\.

## Step 2: Create a Rule<a name="api-create-rule"></a>

Create a rule to run your Lambda function whenever you stop an Amazon EC2 instance\.

**To create a rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**\.

   1. Choose **Build event pattern to match events by service**\.

   1. Choose **EC2**, **AWS API Call via CloudTrail**\.

   1. Choose **Specific operation\(s\)** and then type `StopInstances` in the box below\.

1. For **Targets**, choose **Add target**, **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for the rule\.

1. Choose **Create rule**\.

## Step 3: Test the Rule<a name="api-test-rule"></a>

You can test your rule by stopping an Amazon EC2 instance using the Amazon EC2 console\. After waiting a few minutes for the instance to stop, check your AWS Lambda metrics in the CloudWatch console to verify that your function was invoked\.

**To test your rule by stopping an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Launch an instance\. For more information, see [Launch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Stop the instance\. For more information, see [Stop and Start Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, select the name of the rule that you created, and choose **Show metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. In the navigation pane, choose **Logs**\.

   1. Select the name of the log group for your Lambda function \(/aws/lambda/*function\-name*\)\.

   1. Select the name of log stream to view the data provided by the function for the instance that you stopped\.

1. \(Optional\) When you are finished, you can terminate the stopped instance\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.
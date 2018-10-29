# Tutorial: Log the State of an Amazon EC2 Instance Using CloudWatch Events<a name="LogEC2InstanceState"></a>

You can create an AWS Lambda function that logs the changes in state for an Amazon EC2 instance\. You can choose to create a rule that runs the function whenever there is a state transition or a transition to one or more states that are of interest\. In this tutorial, you log the launch of any new instance\.

## Step 1: Create an AWS Lambda Function<a name="ec2-create-lambda-function"></a>

Create a Lambda function to log the state change events\. You specify this function when you create your rule\.

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. If you are new to Lambda, you see a welcome page\. Choose **Get Started Now**\. Otherwise, choose **Create a Lambda function**\.

1. On the **Select blueprint** page, type `hello` for the filter and choose the **hello\-world** blueprint\.

1. On the **Configure triggers** page, choose **Next**\.

1. On the **Configure function** page, do the following:

   1. Type a name and description for the Lambda function\. For example, name the function "LogEC2InstanceStateChange"\. 

   1. Edit the sample code for the Lambda function\. For example:

      ```
      'use strict';
      
      exports.handler = (event, context, callback) => {
          console.log('LogEC2InstanceStateChange');
          console.log('Received event:', JSON.stringify(event, null, 2));
          callback(null, 'Finished');
      };
      ```

   1. For **Role**, choose **Choose an existing role**\. For **Existing role**, select your basic execution role\. Otherwise, create a new basic execution role\.

   1. Choose **Next**\.

1. On the **Review** page, choose **Create function**\.

## Step 2: Create a Rule<a name="ec2-create-rule"></a>

Create a rule to run your Lambda function whenever you launch an Amazon EC2 instance\.

**To create a CloudWatch Events rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**\.

   1. Choose **Build event pattern to match events by service**\.

   1. Choose **EC2**, **EC2 Instance State\-change Notification**\.

   1. Choose **Specific state\(s\)**, **Running**\.

   1. By default, the rule matches any instance in the region\. To make the rule match a specific instance, choose **Specific instance\(s\)** and then select one or more instances\.  
![\[The Event selector pane\]](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/images/log_stateec2_using_CWE_1.PNG)

1. For **Targets**, choose **Add target**, **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for the rule\.

1. Choose **Create rule**\.

## Step 3: Test the Rule<a name="ec2-test-rule"></a>

To test your rule, launch an Amazon EC2 instance\. After waiting a few minutes for the instance to launch and initialize, you can verify that your Lambda function was invoked\.

**To test your rule by launching an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Launch an instance\. For more information, see [Launch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Rules**, select the name of the rule that you created, and choose**Show metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. In the navigation pane, choose **Logs**\.

   1. Choose the name of the log group for your Lambda function \(/aws/lambda/*function\-name*\)\.

   1. Choose the name of log stream to view the data provided by the function for the instance that you launched\.

1. \(Optional\) When you are finished, you can open the Amazon EC2 console and stop or terminate the instance that you launched\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.
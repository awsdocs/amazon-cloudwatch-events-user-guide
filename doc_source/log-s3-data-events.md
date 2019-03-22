# Tutorial: Log Amazon S3 Object\-Level Operations Using CloudWatch Events<a name="log-s3-data-events"></a>

You can log the object\-level API operations on your S3 buckets\. Before Amazon CloudWatch Events can match these events, you must use AWS CloudTrail to set up a trail configured to receive these events\.

## Step 1: Configure Your AWS CloudTrail Trail<a name="configure-trail"></a>

To log data events for an S3 bucket to AWS CloudTrail and CloudWatch Events, create a trail\. A trail captures API calls and related events in your account and delivers the log files to an S3 bucket that you specify\. You can update an existing trail or create a new one\.

**To create a trail**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. In the navigation pane, choose **Trails**, **Create trail**\.

1. For **Trail name**, type a name for the trail\.

1. For **Data events,** type the bucket name and prefix \(optional\)\. For each trail, you can add up to 250 Amazon S3 objects\.
   + To log data events for all Amazon S3 objects in a bucket, specify an S3 bucket and an empty prefix\. When an event occurs on an object in that bucket, the trail processes and logs the event\.
   + To log data events for specific Amazon S3 objects, choose **Add S3 bucket**, then specify an S3 bucket and optionally the object prefix\. When an event occurs on an object in that bucket and the object starts with the specified prefix, the trail processes and logs the event\.

1. For each resource, specify whether to log **Read** events, **Write** events, or both\.

1. For **Storage location**, create or choose an existing S3 bucket to designate for log file storage\.

1. Choose **Create**\.

For more information, see [Data Events](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-management-and-data-events-with-cloudtrail.html#logging-data-events) in the AWS CloudTrail User Guide\. 

## Step 2: Create an AWS Lambda Function<a name="log-s3-create-lambda-function"></a>

Create a Lambda function to log data events for your S3 buckets\. You specify this function when you create your rule\.

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. If you are new to Lambda, you see a welcome page\. Choose **Create a function**\. Otherwise, choose **Create function**\.

1. Choose **Author from scratch**\. 

1. Under **Author from scratch**, do the following:

   1. Type a name for the Lambda function\. For example, name the function "LogS3DataEvents"\.

   1. For **Role**, choose **Create a custom role**\.

      A new window opens\. Change the **Role name** if necessary, and choose **Allow**\.

   1. Back in the Lambda console, choose **Create function**\.

1. Edit the code for the Lambda function to the following, and choose **Save**\.

   ```
   'use strict';
   
   exports.handler = (event, context, callback) => {
       console.log('LogS3DataEvents');
       console.log('Received event:', JSON.stringify(event, null, 2));
       callback(null, 'Finished');
   };
   ```

## Step 3: Create a Rule<a name="log-s3-create-rule"></a>

Create a rule to run your Lambda function in response to an Amazon S3 data event\.

**To create a rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Rules**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**\.

   1. Choose **Build event pattern to match events by service**\.

   1. Choose **Simple Storage Service \(S3\)**, **Object Level Operations**\.

   1. Choose **Specific operation\(s\)**, **PutObject**\.

   1. By default, the rule matches data events for all buckets in the region\. To match data events for specific buckets, choose **Specify bucket\(s\) by name** and then specify one or more buckets\.

1. For **Targets**, choose **Add target**, **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for the rule\.

1. Choose **Create rule**\.

## Step 4: Test the Rule<a name="log-s3-test-rule"></a>

To test the rule, put an object in your S3 bucket\. You can verify that your Lambda function was invoked\.

**To view the logs for your Lambda function**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. Select the name of the log group for your Lambda function \(/aws/lambda/*function\-name*\)\.

1. Select the name of log stream to view the data provided by the function for the instance that you launched\.

You can also check the contents of your CloudTrail logs in the S3 bucket that you specified for your trail\. For more information, see [Getting and Viewing Your CloudTrail Log Files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/get-and-view-cloudtrail-log-files.html) in the *AWS CloudTrail User Guide*\.
# Tutorial: Schedule AWS Lambda Functions Using CloudWatch Events<a name="RunLambdaSchedule"></a>

You can set up a rule to run an AWS Lambda function on a schedule\. This tutorial shows how to use the AWS Management Console or the AWS CLI to create the rule\. If you would like to use the AWS CLI but have not installed it, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

CloudWatch Events does not provide second\-level precision in schedule expressions\. The finest resolution using a cron expression is a minute\. Due to the distributed nature of the CloudWatch Events and the target services, the delay between the time the scheduled rule is triggered and the time the target service honors the execution of the target resource might be several seconds\. Your scheduled rule is triggered within that minute but not on the precise 0th second\.

## Step 1: Create an AWS Lambda Function<a name="create-lambda-function"></a>

Create a Lambda function to log the scheduled events\. Specify this function when you create your rule\.

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. If you are new to Lambda, you see a welcome page\. Choose **Get Started Now**\. Otherwise, choose **Create a Lambda function**\.

1. On the **Select blueprint** page, type `hello` for the filter and choose the **hello\-world** blueprint\.

1. On the **Configure triggers** page, choose **Next**\.

1. On the **Configure function** page, do the following:

   1. Type a name and description for the Lambda function\. For example, name the function "LogScheduledEvent"\.

   1. Edit the sample code for the Lambda function\. For example:

      ```
      'use strict';
      
      exports.handler = (event, context, callback) => {
          console.log('LogScheduledEvent');
          console.log('Received event:', JSON.stringify(event, null, 2));
          callback(null, 'Finished');
      };
      ```

   1. For **Role**, choose **Choose an existing role**\. For **Existing role**, select your basic execution role\. Otherwise, create a new basic execution role\.

   1. Choose **Next**\.

1. On the **Review** page, choose **Create function**\.

## Step 2: Create a Rule<a name="schedule-create-rule"></a>

Create a rule to run your Lambda function on a schedule\.

**To create a rule using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event Source**, do the following:

   1. Choose **Schedule**\.

   1. Choose **Fixed rate of** and specify the schedule interval \(for example, 5 minutes\)\.

1. For **Targets**, choose **Add target**, **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for the rule\.

1. Choose **Create rule**\.

If you prefer, you can create the rule using the AWS CLI\. First, you must grant the rule permission to invoke your Lambda function\. Then you can create the rule and add the Lambda function as a target\.

**To create a rule using the AWS CLI**

1. Use the following [put\-rule](https://docs.aws.amazon.com/cli/latest/reference/events/put-rule.html) command to create a rule that triggers itself on a schedule:

   ```
   aws events put-rule \
   --name my-scheduled-rule \
   --schedule-expression 'rate(5 minutes)'
   ```

   When this rule triggers, it generates an event that serves as input to the targets of this rule\. The following is an example event:

   ```
   {
       "version": "0",
       "id": "53dc4d37-cffa-4f76-80c9-8b7d4a4d2eaa",
       "detail-type": "Scheduled Event",
       "source": "aws.events",
       "account": "123456789012",
       "time": "2015-10-08T16:53:06Z",
       "region": "us-east-1",
       "resources": [
           "arn:aws:events:us-east-1:123456789012:rule/my-scheduled-rule"
       ],
       "detail": {}
   }
   ```

1. Use the following [add\-permission](https://docs.aws.amazon.com/cli/latest/reference/lambda/add-permission.html) command to trust the CloudWatch Events service principal \(events\.amazonaws\.com\) and scope permissions to the rule with the specified Amazon Resource Name \(ARN\):

   ```
   aws lambda add-permission \
   --function-name LogScheduledEvent \
   --statement-id my-scheduled-event \
   --action 'lambda:InvokeFunction' \
   --principal events.amazonaws.com \
   --source-arn arn:aws:events:us-east-1:123456789012:rule/my-scheduled-rule
   ```

1. Use the following [put\-targets](https://docs.aws.amazon.com/cli/latest/reference/events/put-targets.html) command to add the Lambda function that you created to this rule so that it runs every five minutes:

   ```
   aws events put-targets --rule my-scheduled-rule --targets file://targets.json
   ```

   Create the file `targets.json` with the following contents:

   ```
   [
     {
       "Id": "1", 
       "Arn": "arn:aws:lambda:us-east-1:123456789012:function:LogScheduledEvent"
     }
   ]
   ```

## Step 3: Verify the Rule<a name="schedule-test-rule"></a>

At least five minutes after completing Step 2, you can verify that your Lambda function was invoked\.

**To test your rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Rules**, select the name of the rule that you created, and choose **Show metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. In the navigation pane, choose **Logs**\.

   1. Select the name of the log group for your Lambda function \(/aws/lambda/*function\-name*\)\.

   1. Select the name of log stream to view the data provided by the function for the instance that you launched\.

1. \(Optional\) When you are finished, you can disable the rule\.

   1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   1. In the navigation pane, choose **Events**, **Rules**\.

   1. Select the rule and choose **Actions**, **Disable**\.

   1. When prompted for confirmation, choose **Disable**\.
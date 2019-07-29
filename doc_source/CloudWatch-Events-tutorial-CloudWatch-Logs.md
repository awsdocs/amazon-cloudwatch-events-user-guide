# Tutorial: Log State Changes of Amazon EC2 Instances<a name="CloudWatch-Events-tutorial-CloudWatch-Logs"></a>

In the example in this tutorial, you create a rule causing state\-change notifications in Amazon EC2 to be logged in CloudWatch Logs\.

**To create a rule to log Amazon EC2 state\-change notifications in CloudWatch Logs**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events** and then **Create rule**\.

1. For **Event Source**, do the following:

   1. Choose **Event Pattern**\.

   1. For **Service Name**, choose **EC2**\.

   1. For **Event Type**, choose **EC2 Instance State\-change Notification**\.

1. For **Targets**, choose **Add target**\. In the list of services, choose **CloudWatch log group**\.

1. For **Log Group**, enter a name for the log group to receive the state\-change notifications\.

1. Choose **Configure details**\.

1. For **Rule definition**, enter a name and description for the rule\.

1. Choose **Create rule**\.
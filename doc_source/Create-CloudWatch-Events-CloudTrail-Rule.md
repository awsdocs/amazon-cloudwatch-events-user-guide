# Creating a CloudWatch Events Rule That Triggers on an AWS API Call Using AWS CloudTrail<a name="Create-CloudWatch-Events-CloudTrail-Rule"></a>

To create a rule that triggers on an action by an AWS service that does not emit events, you can base the rule on API calls made by that service, which are recorded by AWS CloudTrail\. CloudTrail generally detects all AWS API calls except those calls that begin with Get, List, or Describe\. For a complete list of APIs you can use as triggers for rules, see [Services Supported by CloudTrail Event History](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events-supported-services.html)\.

**To create a rule that triggers on an API call via CloudTrail:**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**, **Build event pattern to match events by service**\.

   1. For **Service Name**, choose the service that uses the API operations to use as the trigger\.

   1. For **Event Type**, choose **AWS API Call via CloudTrail**\.

   1. To trigger your rule when any API operation for this service is called, choose **Any operation**\. To trigger your rule only when certain API operations are called, choose **Specific operation\(s\)**, type the name of an operation in the next box, and press ENTER\. To add more operations, choose **\+**\.

1. For **Targets**, choose **Add Target**, then choose the AWS service that is to act when an event of the selected type is detected\. 

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, CloudWatch Events needs permission to send events to the target\. In these cases, CloudWatch Events can create the IAM role needed for your event to run: 

   + To create an IAM role automatically, choose **Create a new role for this specific resource\.**

   + To use an IAM role that you created before, choose **Use existing role**\.

1. Optionally, repeat steps 4\-6 to add another target for this rule\.

1. Choose **Configure details**\. For **Rule definition**, type a name and description for the rule\. 

1. Choose **Create rule**\.
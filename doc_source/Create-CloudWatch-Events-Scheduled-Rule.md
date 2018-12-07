# Creating a CloudWatch Events Rule That Triggers on a Schedule<a name="Create-CloudWatch-Events-Scheduled-Rule"></a>

Use the following steps to create a CloudWatch Events rule that triggers on a regular schedule\.

**To create a rule that triggers on a regular schedule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, choose **Schedule**\.

1. Choose **Fixed rate of** and specify how often the task is to run, or choose **Cron expression** and specify a cron expression that defines when the task is to be triggered\. For more information about cron expression syntax, see [Schedule Expressions for Rules](ScheduledEvents.md)\.

1. For **Targets**, choose **Add Target** and choose the AWS service that is to act when an event of the selected type is detected\. 

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, CloudWatch Events needs permissions to send events to the target\. In these cases, CloudWatch Events can create the IAM role needed for your event to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created before, choose **Use existing role**\.

1. Optionally, repeat steps 5\-7 to add another target for this rule\.

1. Choose **Configure details**\. For **Rule definition**, type a name and description for the rule\. 

1. Choose **Create rule**\.
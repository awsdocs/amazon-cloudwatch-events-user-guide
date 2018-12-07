# Creating a CloudWatch Events Rule That Triggers on an Event<a name="Create-CloudWatch-Events-Rule"></a>

Use the following steps to create a CloudWatch Events rule that triggers on an event emitted by an AWS service\.

**To create a rule that triggers on an event:**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**, **Build event pattern to match events by service**\.

   1. For **Service Name**, choose the service that emits the event to trigger the rule\.

   1. For **Event Type**, choose the specific event that is to trigger the rule\. If the only option is ** AWS API Call via CloudTrail**, the selected service does not emit events and you can only base rules on API calls made to this service\. For more information about creating this type of rule, see [Creating a CloudWatch Events Rule That Triggers on an AWS API Call Using AWS CloudTrail](Create-CloudWatch-Events-CloudTrail-Rule.md)\.

   1. Depending on the service emitting the event, you may see options for **Any\.\.\.** and **Specific\.\.\.**\. Choose **Any\.\.\.** to have the event trigger on any type of the selected event, or choose **Specific\.\.\.** to choose one or more specific event types\.

1. For **Targets**, choose **Add Target** and choose the AWS service that is to act when an event of the selected type is detected\. 

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, CloudWatch Events needs permissions to send events to the target\. In these cases, CloudWatch Events can create the IAM role needed for your event to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created before, choose **Use existing role**\.

1. Optionally, repeat steps 4\-6 to add another target for this rule\.

1. Choose **Configure details**\. For **Rule definition**, type a name and description for the rule\. 

1. Choose **Create rule**\.
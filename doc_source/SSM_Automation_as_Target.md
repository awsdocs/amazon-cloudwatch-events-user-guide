# Tutorial: Set AWS Systems Manager Automation as a CloudWatch Events Target<a name="SSM_Automation_as_Target"></a>

You can use CloudWatch Events to invoke AWS Systems Manager Automation on a regular timed schedule, or when specified events are detected\. This tutorial assumes that you are invoking Systems Manager Automation based on certain events\.

**To create the CloudWatch Events rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern** and choose **Build event pattern to match events by service**\. 

   1. For **Service Name** and **Event Type**, choose the service and event type to use as the trigger\.

      Depending on the service and event type you choose, you may need to specify additional options under **Event Source**\.

1. For **Targets**, choose **Add Target**, **SSM Automation\.** 

1. For **Document**, choose the Systems Manager document to run when the target is triggered\.

1. \(Optional\), To specify a certain version of the document, choose **Configure document version**\.

1. Under **Configure parameter\(s\)**, choose **No Parameter\(s\)** or **Constant**\.

   If you choose **Constant**, specify the constants to pass to the document execution\.

1. CloudWatch Events can create the IAM role needed for your event to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created before, choose **Use existing role**\.

1. Choose **Configure details**\. For **Rule definition**, type a name and description for the rule\. 

1. Choose **Create rule**\.
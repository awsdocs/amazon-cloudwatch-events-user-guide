# Tutorial: Schedule Automated Amazon EBS Snapshots Using CloudWatch Events<a name="TakeScheduledSnapshot"></a>

You can run CloudWatch Events rules according to a schedule\. In this tutorial, you create an automated snapshot of an existing Amazon Elastic Block Store \(Amazon EBS\) volume on a schedule\. You can choose a fixed rate to create a snapshot every few minutes or use a cron expression to specify that the snapshot is made at a specific time of day\.

**Important**  
Creating rules with built\-in targets is supported only in the AWS Management Console\.

## Step 1: Create a Rule<a name="ebs-create-rule"></a>

Create a rule that takes snapshots on a schedule\. You can use a rate expression or a cron expression to specify the schedule\. For more information, see [Schedule Expressions for Rules](ScheduledEvents.md)\.

**To create a rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event Source**, do the following:

   1. Choose **Schedule**\.

   1. Choose **Fixed rate of** and specify the schedule interval \(for example, 5 minutes\)\. Alternatively, choose **Cron expression** and specify a cron expression \(for example, every 15 minutes Monday through Friday, starting at the current time\)\.

1. For **Targets**, choose **Add target** and then select **EC2 CreateSnapshot API call**\. You may have to scroll up in the list of possible targets to find **EC2 CreateSnapshot API call**\.

1. For **Volume ID**, type the volume ID of the targeted Amazon EBS volume\.

1. Choose **Create a new role for this specific resource**\. The new role grants the target permissions to access resources on your behalf\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for the rule\.

1. Choose **Create rule**\.

## Step 2: Test the Rule<a name="ebs-test-rule"></a>

You can verify your rule by viewing your first snapshot after it is taken\.

**To test your rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Snapshots**\.

1. Verify that the first snapshot appears in the list\.

1. \(Optional\) When you are finished, you can disable the rule to prevent additional snapshots from being taken\.

   1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   1. In the navigation pane, choose **Events**, **Rules**\.

   1. Select the rule and choose **Actions**, **Disable**\.

   1. When prompted for confirmation, choose **Disable**\.
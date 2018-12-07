# Deleting or Disabling a CloudWatch Events Rule<a name="Delete-or-Disable-Rule"></a>

Use the following steps to delete or disable a CloudWatch Events rule\.

**To delete or disable a rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Rules**\.

   Managed rules have a box icon next to their names\. For more information, see [Amazon CloudWatch Events Managed Rules](CloudWatch-Events-Managed-Rules.md)\.

1. Do one of the following:

   1. To delete a rule, select the button next to the rule and choose **Actions**, **Delete**, **Delete**\.

      If the rule is a managed rule, you must type the name of the rule to acknowledge that it is a managed rule, and that deleting it may stop functionality in the service that created the rule\. To continue, type the rule name and choose **Force delete**\.

   1. To temporarily disable a rule, select the button next to the rule and choose **Actions**, **Disable**, **Disable**\.

      You cannot disable a managed rule\.
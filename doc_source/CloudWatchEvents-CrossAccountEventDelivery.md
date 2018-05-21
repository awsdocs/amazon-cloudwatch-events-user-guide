# Sending and Receiving Events Between AWS Accounts<a name="CloudWatchEvents-CrossAccountEventDelivery"></a>

You can set up your AWS account to send events to another AWS account, or to receive events from another account\. This can be useful if the two accounts belong to the same organization, or belong to organizations that are partners or have a similar relationship\.

If you set up your account to send or receive events, you can specify which AWS accounts it sends events to or receives events from\.

The overall process is as follows:
+ Edit the *receiver* account permissions on its default *event bus* to allow one or more specified accounts \(or all AWS accounts\) to send events to the receiver account\.
+ On the *sender* account, set up one or more rules that have the receiver account's default event bus as the target\.
+ On the *receiver* account, set up one or more rules that match events that come from the sender account\.

The AWS Region where the receiver account adds permissions to the default event bus must be the same region where the sender account creates the rule to send events to the receiver account\.

Events sent from one account to another are charged to the sending account as custom events\. The receiving account is not charged\. For more information about CloudWatch Events pricing, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

 A receiver account can set up a rule that sends events received from a sender account on to a third account, however these events are not sent to the third account\.

## Enabling Your AWS Account to Receive Events from Other AWS Accounts<a name="ReceivingEventsFromAnotherAccount"></a>

To receive events from other accounts, you must first edit the permissions on your account's default *event bus*\. The default event bus accepts events from AWS services, other authorized AWS accounts, and `PutEvents` calls\.

When you edit the permissions on your default event bus to grant permission to other AWS accounts, you can specify accounts by account ID\. Or you can choose to receive events from all AWS accounts\.

**Warning**  
If you choose to receive events from all AWS accounts, be careful to create rules that match only events you want to receive from others\. To create more secure rules, be sure the event pattern for each rule contains an `account` field with the account IDs of one or more accounts that you want to receive events from\. Rules that have an event pattern containing an account field do not match events sent from other accounts\. For more information, see [Event Patterns in CloudWatch Events](CloudWatchEventsandEventPatterns.md)\.

**To enable your account to receive events from other AWS accounts using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Event Buses**, **Add Permission**\.

1. For **Principal**, type the 12\-digit AWS Account ID of the account from which to receive events\. To receive events from all other AWS accounts, choose **Everybody\(\*\)**\.

1. Choose **Add**\.

**To enable your account to receive events from other AWS accounts using the AWS CLI**

1. To enable one specific AWS account to send events, run the following command:

   ```
   aws events put-permission --action events:PutEvents --statement-id MySid --principal SenderAccountID
   ```

   To enable all other AWS accounts to send events, run the following command:

   ```
   aws events put-permission --action events:PutEvents --statement-id MySid --principal \*
   ```

1. After setting permissions for your default event bus, you can optionally use the `describe-event-bus` command to check the permissions\.

   ```
   aws events describe-event-bus
   ```

## Sending Events to Another AWS Account<a name="SendingEventsToAnotherAccount"></a>

To send events to another account, you configure a CloudWatch Events rule that has the default event bus of another AWS account as the target\. The default event bus of that receiving account must also be configured to receive events from your account\.

**To send events from your account to another AWS account using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create Rule**\.

1. For **Event Source**, choose **Event Pattern**, and then choose the service name and event types to send to the other account\.

1. Choose **Add Target**\.

1. In the drop\-down list, select **Event bus in another AWS account**\. Then in **Account ID**, type the 12\-digit account ID of the AWS account to which to send events\.

1. At the bottom of the page, choose **Configure Details**\.

1. Type a name and description for the rule, and choose **Create Rule **\.

**To send events to another AWS account using the AWS CLI**

1. Use the `put-rule` command to create a rule that matches the event types to send to the other account\.

1. Add the other account's default event bus as the target of the rule:

   ```
   aws events put-targets --rule NameOfRuleMatchingEventsToSend --targets "Id"="MyId","Arn"="arn:aws:events:region:$ReceiverAccountID:event-bus/default"
   ```

## Writing Rules that Match Events from Another AWS Account<a name="WritingRulesThatMatchEventsFromAnotherAccount"></a>

If your account is set up to receive events from other AWS accounts, you can write rules that match those events\. Set the event pattern of the rule to match the events you are receiving from the other account\.

Unless you specify `Account` in the event pattern of a rule, any of your account's rules, both new and existing, that match events you receive from other accounts will trigger based on those events\. If you are receiving events from another account, and you want a rule to trigger only on that event pattern when it is generated from your own account, you must add `Account` and specify your own account ID to the event pattern of the rule\.

If you set up your AWS account to accept events from all AWS accounts, we strongly recommend that you add `Account` to every CloudWatch Events rule in your account\. This prevents rules in your account from triggering on events from unknown AWS accounts\. When you specify the `Account` field in the rule, you can specify the account IDs of more than one AWS account in the field\.

**To write a rule matching events from another account using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create Rule**\.

1. For **Event Source**, choose **Event Pattern**, and select the service name and event types that the rule should match\.

1. Near **Event Pattern Preview**, choose **Edit**\.

1. In the edit window, add an `Account` line specifying what AWS accounts that send this event should be matched by the rule\. For example, the edit window originally shows the following:

   ```
   {
     "source": [
       "aws.ec2"
     ],
     "detail-type": [
       "EBS Volume Notification"
     ]
   }
   ```

   You could add the following to make the rule match EBS volume notifications that are sent by the AWS accounts 123456789012 and 111122223333:

   ```
   {
     "account": [
       "123456789012","111122223333"
     ],
     "source": [
       "aws.ec2"
     ],
     "detail-type": [
       "EBS Volume Notification"
     ]
   }
   ```

1. After editing the event pattern, choose **Save**\. 

1. Finish creating the rule as usual, setting one or more targets in your account\.

**To write a rule matching events from another AWS account using the AWS CLI**
+ Use the `put-rule` command and specify the other AWS accounts that the rule is to match in the `Account` field in the rule's event pattern\. The following example rule matches Amazon EC2 instance state changes in the AWS accounts 123456789012 and 111122223333:

  ```
  aws events put-rule --name "EC2InstanceStateChanges" --event-pattern "{\"account\":["123456789012", "111122223333"],\"source\":[\"aws.ec2\"],\"detail-type\":[\"EC2 Instance State-change Notification\"]}"  --role-arn "arn:aws:iam::123456789012:role/MyRoleForThisRule"
  ```
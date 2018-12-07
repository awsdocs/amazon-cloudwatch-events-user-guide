# Sending and Receiving Events Between AWS Accounts<a name="CloudWatchEvents-CrossAccountEventDelivery"></a>

You can set up your AWS account to send events to other AWS accounts, or to receive events from other accounts\. This can be useful if the accounts belong to the same organization, or belong to organizations that are partners or have a similar relationship\.

If you set up your account to send or receive events, you specify which individual AWS accounts can send events to or receive events from yours\. If you use the AWS Organizations feature, you can specify an organization and grant access to all accounts in that organization\. For more information, see [What is AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html) in the *AWS Organizations User Guide*\.

The overall process is as follows:
+ On the *receiver* account, edit the permissions on the default *event bus* to allow specified AWS accounts, an organization, or all AWS accounts to send events to the receiver account\.
+ On the *sender* account, set up one or more rules that have the receiver account's default event bus as the target\.

  If the sender account has permissions to send events because it is part of an AWS organization that has permissions, the sender account also must have an IAM role with policies that enable it to send events to the receiver account\. If you use the AWS Management Console to create the rule that targets the receiver account, this is done automatically\. If you use the AWS CLI, you must create the role manually\.
+ On the *receiver* account, set up one or more rules that match events that come from the sender account\.

The AWS Region where the receiver account adds permissions to the default event bus must be the same region where the sender account creates the rule to send events to the receiver account\.

Events sent from one account to another are charged to the sending account as custom events\. The receiving account is not charged\. For more information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

If a receiver account sets up a rule that sends events received from a sender account on to a third account, these events are not sent to the third account\.

## Enabling Your AWS Account to Receive Events from Other AWS Accounts<a name="ReceivingEventsFromAnotherAccount"></a>

To receive events from other accounts or organizations, you must first edit the permissions on your account's default *event bus*\. The default event bus accepts events from AWS services, other authorized AWS accounts, and `PutEvents` calls\.

When you edit the permissions on your default event bus to grant permission to other AWS accounts, you can specify accounts by account ID or organization ID\. Or you can choose to receive events from all AWS accounts\.

**Warning**  
If you choose to receive events from all AWS accounts, be careful to create rules that match only the events to receive from others\. To create more secure rules, make sure that the event pattern for each rule contains an `Account` field with the account IDs of one or more accounts from which to receive events\. Rules that have an event pattern containing an Account field do not match events sent from other accounts\. For more information, see [Event Patterns in CloudWatch Events](CloudWatchEventsandEventPatterns.md)\.

**To enable your account to receive events from other AWS accounts using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Event Buses**, **Add Permission**\.

1. Choose **AWS Account** or **Organization**\.

   If you choose **AWS Account**, enter the 12\-digit AWS account ID of the account from which to receive events\. To receive events from all other AWS accounts, choose **Everybody\(\*\)**\.

   If you choose **Organization**, choose **My organization** to grant permissions to all accounts in the organization of which the current account is a member\. Or choose **Another organization** and enter the organization ID of that organization\. You must include the `o-` prefix when you type the organization ID\.

1. Choose **Add**\.

1. You can repeat these steps to add other accounts or organizations\.

**To enable your account to receive events from other AWS accounts using the AWS CLI**

1. To enable one specific AWS account to send events, run the following command:

   ```
   aws events put-permission --action events:PutEvents --statement-id MySid --principal SenderAccountID
   ```

   To enable an AWS organization to send events, run the following command:

   ```
   aws events put-permission --action events:PutEvents --statement-id MySid 
                     --principal \* --condition '{"Type" : "StringEquals", "Key": "aws:PrincipalOrgID", "Value": "SenderOrganizationID"}'
   ```

   To enable all other AWS accounts to send events, run the following command:

   ```
   aws events put-permission --action events:PutEvents --statement-id MySid --principal \*
   ```

   You can run `aws events put-permission` multiple times to grant permissions to both individual AWS accounts and organizations, but you cannot specify both an individual account and an organization in a single command\.

1. After setting permissions for your default event bus, you can optionally use the `describe-event-bus` command to check the permissions:

   ```
   aws events describe-event-bus
   ```

## Sending Events to Another AWS Account<a name="SendingEventsToAnotherAccount"></a>

To send events to another account, configure a CloudWatch Events rule that has the default event bus of another AWS account as the target\. The default event bus of that receiving account must also be configured to receive events from your account\.

**To send events from your account to another AWS account using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create Rule**\.

1. For **Event Source**, choose **Event Pattern** and select the service name and event types to send to the other account\.

1. Choose **Add Target**\.

1. For **Target**, choose **Event bus in another AWS account**\. For **Account ID**, enter the 12\-digit account ID of the AWS account to which to send events\.

1. An IAM role is needed when this sender account has permissions to send events because the receiver account granted permissions to an entire organization\.
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + Otherwise, choose **Use existing role**\. Choose a role that already has sufficient permissions to invoke the build\. CloudWatch Events does not grant additional permissions to the role that you select\.

1. At the bottom of the page, choose **Configure Details**\.

1. Type a name and description for the rule, and choose **Create Rule **\.

**To send events to another AWS account using the AWS CLI**

1. If the sender account has permissions to send events because it is part of an AWS organization to which the receiver account has granted permissions, the sender account also must have a role with policies that enable it to send events to the receiver account\. This step explains how to create that role\.

   If the sender account was given permission to send events by way of its AWS account ID, and not through an organization, this step is optional\. You can skip to step 2\.

   1. If the sender account was granted permissions through an organization, create the IAM role needed\. First, create a file named `assume-role-policy-document.json`, with the following content:

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "events.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }
        ]
      }
      ```

   1. To create the role, enter the following command:

      ```
      $ aws iam create-role \
      --profile sender \
      --role-name event-delivery-role \
      --assume-role-policy-document file://assume-role-policy-document.json
      ```

   1. Create a file named `permission-policy.json` with the following content:

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "events:PutEvents"
            ],
            "Resource": [
              "arn:aws:events:us-east-1:${receiver_account_id}:event-bus/default"
            ]
          }
        ]
      }
      ```

   1. Enter the following command to attach this policy to the role:

      ```
      $ aws iam put-role-policy \
      --profile sender \
      --role-name event-delivery-role \
      --policy-name EventBusDeliveryRolePolicy
      --policy-document file://permission-policy.json
      ```

1. Use the `put-rule` command to create a rule that matches the event types to send to the other account\.

1. Add the other account's default event bus as the target of the rule\.

   If the sender account was given permissions to send events by its account ID, specifying a role is not necessary\. Run the following command:

   ```
   aws events put-targets --rule NameOfRuleMatchingEventsToSend --targets "Id"="MyId","Arn"="arn:aws:events:region:$ReceiverAccountID:event-bus/default"
   ```

   If the sender account was given permissions to send events by its organization, specify a role, as in the following example:

   ```
   aws events put-targets --rule NameOfRuleMatchingEventsToSend --targets "Id"="MyId","Arn"="arn:aws:events:region:$ReceiverAccountID:event-bus/default","RoleArn"="arn:aws:iam:${sender_account_id}:role/event-delivery-role"
   ```

## Writing Rules that Match Events from Another AWS Account<a name="WritingRulesThatMatchEventsFromAnotherAccount"></a>

If your account is set up to receive events from other AWS accounts, you can write rules that match those events\. Set the event pattern of the rule to match the events you are receiving from the other account\.

Unless you specify `account` in the event pattern of a rule, any of your account's rules, both new and existing, that match events you receive from other accounts trigger based on those events\. If you are receiving events from another account, and you want a rule to trigger only on that event pattern when it is generated from your own account, you must add `account` and specify your own account ID to the event pattern of the rule\.

If you set up your AWS account to accept events from all AWS accounts, we strongly recommend that you add `account` to every CloudWatch Events rule in your account\. This prevents rules in your account from triggering on events from unknown AWS accounts\. When you specify the `account` field in the rule, you can specify the account IDs of more than one AWS account in the field\.

To have a rule trigger on a matching event from any AWS account that you have granted permissions to, do not specify \* in the `account` field of the rule\. Doing so would not match any events, because \* never appears in the `account` field of an event\. Instead, just omit the `account` field from the rule\.

**To write a rule matching events from another account using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create Rule**\.

1. For **Event Source**, choose **Event Pattern**, and select the service name and event types that the rule should match\.

1. Near **Event Pattern Preview**, choose **Edit**\.

1. In the edit window, add an `Account` line specifying which AWS accounts sending this event should be matched by the rule\. For example, the edit window originally shows the following:

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

   Add the following to make the rule match EBS volume notifications that are sent by the AWS accounts 123456789012 and 111122223333:

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
+ Use the `put-rule` command\. In the `Account` field in the rule's event pattern, specify the other AWS accounts for the rule to match\. The following example rule matches Amazon EC2 instance state changes in the AWS accounts 123456789012 and 111122223333:

  ```
  aws events put-rule --name "EC2InstanceStateChanges" --event-pattern "{\"account\":["123456789012", "111122223333"],\"source\":[\"aws.ec2\"],\"detail-type\":[\"EC2 Instance State-change Notification\"]}"  --role-arn "arn:aws:iam::123456789012:role/MyRoleForThisRule"
  ```

## Migrate a Sender\-Receiver Relationship to Use AWS Organizations<a name="MigratingRulesForOrganizations"></a>

If you have a sender account that had permissions granted directly to its account ID, and you now want to revoke those permissions and give the sending account access by granting permissions to an organization, you must take some additional steps\. These steps ensure that the events from the sender account can still get to the receiver account\. This is because accounts that are given permission to send events via an organization must also use an IAM role to do so\.

**To add the permissions necessary to migrate a sender\-receiver relationship**

1. In the sender account, create an IAM role with policies that enable it to send events to the receiver account\. 

   1. Create a file named `assume-role-policy-document.json`, with the following content:

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "events.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }
        ]
      }
      ```

   1. To create the IAM role, enter the following command:

      ```
      $ aws iam create-role \
      --profile sender \
      --role-name event-delivery-role \
      --assume-role-policy-document file://assume-role-policy-document.json
      ```

   1. Create a file named `permission-policy.json` with the following content:

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "events:PutEvents"
            ],
            "Resource": [
              "arn:aws:events:us-east-1:${receiver_account_id}:event-bus/default"
            ]
          }
        ]
      }
      ```

   1. Enter the following command to attach this policy to the role:

      ```
      $ aws iam put-role-policy \
      --profile sender \
      --role-name event-delivery-role \
      --policy-name EventBusDeliveryRolePolicy
      --policy-document file://permission-policy.json
      ```

1. Edit each existing rule in the sender account that has the receiver account default event bus as a target\. Edit the rule by adding the role that you created in step 1 to the target information\. Use the following command:

   ```
   aws events put-targets --rule Rulename --targets "Id"="MyID","Arn"="arn:aws:events:region:$ReceiverAccountID:event-bus/default","RoleArn"="arn:aws:iam:${sender_account_id}:role/event-delivery-role"
   ```

1. In the receiver account, run the following command to grant permissions for the accounts in the organization to send events to the receiver account:

   ```
   aws events put-permission --action events:PutEvents --statement-id Sid-For-Organization --principal \* --condition '{"Type" : "StringEquals", "Key": "aws:PrincipalOrgID", "Value": "SenderOrganizationID"}'
   ```

   Optionally, you can also revoke the permissions originally granted directly to the sender account:

   ```
   aws events remove-permission --statement-id Sid-for-SenderAccount
   ```
# Troubleshooting CloudWatch Events<a name="CWE_Troubleshooting"></a>

You can use the steps in this section to troubleshoot CloudWatch Events\.

**Topics**
+ [My rule was triggered but my Lambda function was not invoked](#LAMfunctionNotInvoked)
+ [I have just created/modified a rule but it did not match a test event](#RuleDoesNotMatch)
+ [My rule did not self\-trigger at the time specified in the ScheduleExpression](#RuleDidNotTrigger)
+ [My rule did not trigger at the time that I expected](#RuleDidNotTriggerOntime)
+ [My rule matches IAM API calls but my rule was not triggered](#RuleDidNotTriggerIAM)
+ [My rule is not working because the IAM role associated with the rule is ignored when the rule is triggered](#IAMRoleIgnored)
+ [I created a rule with an EventPattern that is supposed to match a resource, but I don't see any events that match the rule](#EventsDoNotMatchRule)
+ [My event's delivery to the target experienced a delay](#DelayedEventDelivery)
+ [Some events were never delivered to my target](#NeverDeliveredToTarget)
+ [My rule was triggered more than once in response to one event\. What guarantee does CloudWatch Events offer for triggering rules or delivering events to the targets?](#RuleTriggeredMoreThanOnce)
+ [Preventing Infinite Loops](#PreventInfiniteLoops)
+ [My events are not delivered to the target Amazon SQS queue](#SQSEncrypted)
+ [My rule is being triggered but I don't see any messages published into my Amazon SNS topic](#NoMessagesPublishedSNS)
+ [My Amazon SNS topic still has permissions for CloudWatch Events even after I deleted the rule associated with the Amazon SNS topic](#SNSPermissionsPersist)
+ [Which IAM condition keys can I use with CloudWatch Events](#SupportedAccessPolicies)
+ [How can I tell when CloudWatch Events rules are broken](#CreateAlarmBrokenEventRules)

## My rule was triggered but my Lambda function was not invoked<a name="LAMfunctionNotInvoked"></a>

Make sure you have the right permissions set for your Lambda function\. Run the following command using AWS CLI \(replace the function name with your function and use the AWS Region your function is in\):

```
aws lambda get-policy --function-name MyFunction --region us-east-1
```

You should see an output similar to the following:

```
{
    "Policy": "{\"Version\":\"2012-10-17\",
    \"Statement\":[
        {\"Condition\":{\"ArnLike\":{\"AWS:SourceArn\":\"arn:aws:events:us-east-1:123456789012:rule/MyRule\"}},
        \"Action\":\"lambda:InvokeFunction\",
        \"Resource\":\"arn:aws:lambda:us-east-1:123456789012:function:MyFunction\",
        \"Effect\":\"Allow\",
        \"Principal\":{\"Service\":\"events.amazonaws.com\"},
        \"Sid\":\"MyId\"}
    ],
    \"Id\":\"default\"}"
}
```

If you see the following:

```
A client error (ResourceNotFoundException) occurred when calling the GetPolicy operation: The resource you requested does not exist.
```

Or, you see the output but you can't locate events\.amazonaws\.com as a trusted entity in the policy, run the following command:

```
aws lambda add-permission \
--function-name MyFunction \
--statement-id MyId \
--action 'lambda:InvokeFunction' \
--principal events.amazonaws.com \
--source-arn arn:aws:events:us-east-1:123456789012:rule/MyRule
```

**Note**  
If the policy is incorrect, you can also edit the rule in the CloudWatch Events console by removing and then adding it back to the rule\. The CloudWatch Events console will set the correct permissions on the target\.  
If you're using a specific Lambda alias or version, you must add the `--qualifier` parameter in the `aws lambda get-policy` and `aws lambda add-permission` commands\.   

```
aws lambda add-permission \
--function-name MyFunction \
--statement-id MyId \
--action 'lambda:InvokeFunction' \
--principal events.amazonaws.com \
--source-arn arn:aws:events:us-east-1:123456789012:rule/MyRule
--qualifier alias or version
```

Another reason the Lambda function would fail to trigger is if the policy you see when running `get-policy` contains a `SourceAccount` field\. A `SourceAccount` setting prevents CloudWatch Events from being able to invoke the function\.

## I have just created/modified a rule but it did not match a test event<a name="RuleDoesNotMatch"></a>

When you make a change to a rule or to its targets, incoming events might not immediately start or stop matching to new or updated rules\. Allow a short period of time for changes to take effect\. If, after this short period, events still do not match, you can also check CloudWatch metrics for your rule such as `TriggeredRules`, `Invocations`, and `FailedInvocations` for further debugging\. For more information about these metrics, see [Amazon CloudWatch Events Metrics and Dimensions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cwe-metricscollected.html) in the *Amazon CloudWatch User Guide*\.

If the rule is triggered by an event from an AWS service, you can also use the `TestEventPattern` action to test the event pattern of your rule with a test event to make sure the event pattern of your rule is correctly set\. For more information, see [TestEventPattern](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TestEventPattern.html) in the *Amazon CloudWatch Events API Reference*\.

## My rule did not self\-trigger at the time specified in the ScheduleExpression<a name="RuleDidNotTrigger"></a>

ScheduleExpressions are in UTC\. Make sure you have set the schedule for rule to self\-trigger in the UTC timezone\. If the ScheduleExpression is correct, then follow the steps under [I have just created/modified a rule but it did not match a test event](#RuleDoesNotMatch)\.

## My rule did not trigger at the time that I expected<a name="RuleDidNotTriggerOntime"></a>

CloudWatch Events doesn't support setting an exact start time when you create a rule to run every time period\. The count down to run time begins as soon as you create the rule\.

You can use a cron expression to invoke targets at a specified time\. For example, you can use a cron expression to create a rule that is triggered every 4 hours exactly on 0 minute\. In the CloudWatch console, you'd use the cron expression `0 0/4 * * ? *`, and with the AWS CLI you'd use the cron expression `cron(0 0/4 * * ? *)`\. For example, to create a rule named TestRule that is triggered every 4 hours using the AWS CLI, you would type the following at a command prompt:

```
aws events put-rule --name TestRule --schedule-expression 'cron(0 0/4 * * ? *)'
```

You can use the `0/5 * * * ? *` cron expression to trigger a rule every 5 minutes\. For example:

```
aws events put-rule --name TestRule --schedule-expression 'cron(0/5 * * * ? *)'
```

CloudWatch Events does not provide second\-level precision in schedule expressions\. The finest resolution using a cron expression is a minute\. Due to the distributed nature of the CloudWatch Events and the target services, the delay between the time the scheduled rule is triggered and the time the target service honors the execution of the target resource might be several seconds\. Your scheduled rule will be triggered within that minute but not on the precise 0th second\.

## My rule matches IAM API calls but my rule was not triggered<a name="RuleDidNotTriggerIAM"></a>

The IAM service is only available in the US East \(N\. Virginia\) Region, so any AWS API call events from IAM are only available in that region\. For more information, see [CloudWatch Events Event Examples From Supported Services](EventTypes.md)\.

## My rule is not working because the IAM role associated with the rule is ignored when the rule is triggered<a name="IAMRoleIgnored"></a>

IAM roles for rules are only used for relating events to Kinesis streams\. For Lambda functions and Amazon SNS topics, you need to provide resource\-based permissions\.

Make sure your regional AWS STS endpoints are enabled\. CloudWatch Events talks to the regional AWS STS endpoints when assuming the IAM role you provided\. For more information, see [Activating and Deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *IAM User Guide*\.

## I created a rule with an EventPattern that is supposed to match a resource, but I don't see any events that match the rule<a name="EventsDoNotMatchRule"></a>

Most services in AWS treat the colon \(:\) or forward slash \(/\) as the same character in Amazon Resource Names \(ARNs\)\. However, CloudWatch Events uses an exact match in event patterns and rules\. Be sure to use the correct ARN characters when creating event patterns so that they match the ARN syntax in the event to match\.

Moreover, not every event has the resources field populated \(such as AWS API call events from CloudTrail\)\.

## My event's delivery to the target experienced a delay<a name="DelayedEventDelivery"></a>

CloudWatch Events tries to deliver an event to a target for up to 24 hours, except in scenarios where your target resource is constrained\. The first attempt is made as soon as the event arrives in the event stream\. However, if the target service is having problems, CloudWatch Events automatically reschedules another delivery in the future\. If 24 hours has passed since the arrival of event, no more attempts are scheduled and the `FailedInvocations` metric is published in CloudWatch\. We recommend that you create a CloudWatch alarm on the `FailedInvocations` metric\.

## Some events were never delivered to my target<a name="NeverDeliveredToTarget"></a>

If a target of a CloudWatch Events rule is constrained for a prolonged time, CloudWatch Events may not retry delivery\. For example, if the target is not provisioned to handle the incoming event traffic and the target service is throttling the requests that CloudWatch Events makes on your behalf, then CloudWatch Events may not retry delivery\.

## My rule was triggered more than once in response to one event\. What guarantee does CloudWatch Events offer for triggering rules or delivering events to the targets?<a name="RuleTriggeredMoreThanOnce"></a>

In rare cases, the same rule can be triggered more than once for a single event or scheduled time, or the same target can be invoked more than once for a given triggered rule\.

## Preventing Infinite Loops<a name="PreventInfiniteLoops"></a>

In CloudWatch Events, it is possible to create rules that lead to infinite loops, where a rule is fired repeatedly\. For example, a rule might detect that ACLs have changed on an S3 bucket, and trigger software to change them to the desired state\. If the rule is not written carefully, the subsequent change to the ACLs fires the rule again, creating an infinite loop\.

To prevent this, write the rules so that the triggered actions do not re\-fire the same rule\. For example, your rule could fire only if ACLs are found to be in a bad state, instead of after any change\. 

An infinite loop can quickly cause higher than expected charges\. We recommend that you use budgeting, which alerts you when charges exceed your specified limit\. For more information, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html)\.

## My events are not delivered to the target Amazon SQS queue<a name="SQSEncrypted"></a>

The Amazon SQS queue may be encrypted\. If you create a rule with an encrypted Amazon SQS queue as a target, you must have the following section included in your KMS key policy for the event to be successfully delivered to the encrypted queue\.

```
{
                "Sid": "Allow CWE to use the key",
                "Effect": "Allow",
                "Principal": {
                                "Service": "events.amazonaws.com"
                },
                "Action": [
                                "kms:Decrypt",
                                "kms:GenerateDataKey"
                ],
                "Resource": "*"
}
```

## My rule is being triggered but I don't see any messages published into my Amazon SNS topic<a name="NoMessagesPublishedSNS"></a>

Make sure you have the right permission set for your Amazon SNS topic\. Run the following command using AWS CLI \(replace the topic ARN with your topic and use the AWS Region your topic is in\):

```
aws sns get-topic-attributes --region us-east-1 --topic-arn "arn:aws:sns:us-east-1:123456789012:MyTopic"
```

You should see policy attributes similar to the following:

```
"{\"Version\":\"2012-10-17\",
\"Id\":\"__default_policy_ID\",
\"Statement\":[{\"Sid\":\"__default_statement_ID\",
\"Effect\":\"Allow\",
\"Principal\":{\"AWS\":\"*\"},
\"Action\":[\"SNS:Subscribe\",
\"SNS:ListSubscriptionsByTopic\",
\"SNS:DeleteTopic\",
\"SNS:GetTopicAttributes\",
\"SNS:Publish\",
\"SNS:RemovePermission\",
\"SNS:AddPermission\",
\"SNS:Receive\",
\"SNS:SetTopicAttributes\"],
\"Resource\":\"arn:aws:sns:us-east-1:123456789012:MyTopic\",
\"Condition\":{\"StringEquals\":{\"AWS:SourceOwner\":\"123456789012\"}}},{\"Sid\":\"Allow_Publish_Events\",
\"Effect\":\"Allow\",
\"Principal\":{\"Service\":\"events.amazonaws.com\"},
\"Action\":\"sns:Publish\",
\"Resource\":\"arn:aws:sns:us-east-1:123456789012:MyTopic\"}]}"
```

If you see a policy similar to the following, you have only the default policy set:

```
"{\"Version\":\"2008-10-17\",
\"Id\":\"__default_policy_ID\",
\"Statement\":[{\"Sid\":\"__default_statement_ID\",
\"Effect\":\"Allow\",
\"Principal\":{\"AWS\":\"*\"},
\"Action\":[\"SNS:Subscribe\",
\"SNS:ListSubscriptionsByTopic\",
\"SNS:DeleteTopic\",
\"SNS:GetTopicAttributes\",
\"SNS:Publish\",
\"SNS:RemovePermission\",
\"SNS:AddPermission\",
\"SNS:Receive\",
\"SNS:SetTopicAttributes\"],
\"Resource\":\"arn:aws:sns:us-east-1:123456789012:MyTopic\",
\"Condition\":{\"StringEquals\":{\"AWS:SourceOwner\":\"123456789012\"}}}]}"
```

If you don't see `events.amazonaws.com` with Publish permission in your policy, use the AWS CLI to set topic policy attribute\.

Copy the current policy and add the following statement to the list of statements:

```
{\"Sid\":\"Allow_Publish_Events\",
\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"events.amazonaws.com\"},
\"Action\":\"sns:Publish\",
\"Resource\":\"arn:aws:sns:us-east-1:123456789012:MyTopic\"}
```

The new policy should look like the one described earlier\.

Set topic attributes with the AWS CLI:

```
aws sns set-topic-attributes --region us-east-1 --topic-arn "arn:aws:sns:us-east-1:123456789012:MyTopic" --attribute-name Policy --attribute-value NEW_POLICY_STRING
```

**Note**  
If the policy is incorrect, you can also edit the rule in the CloudWatch Events console by removing and then adding it back to the rule\. CloudWatch Events sets the correct permissions on the target\.

## My Amazon SNS topic still has permissions for CloudWatch Events even after I deleted the rule associated with the Amazon SNS topic<a name="SNSPermissionsPersist"></a>

When you create a rule with Amazon SNS as the target, CloudWatch Events adds the permission to your Amazon SNS topic on your behalf\. If you delete the rule shortly after you create it, CloudWatch Events might be unable to remove the permission from your Amazon SNS topic\. If this happens, you can remove the permission from the topic using the [aws sns set\-topic\-attributes](https://docs.aws.amazon.com/cli/latest/reference/sns/set-topic-attributes.html) command\. For more information about resource\-based permissions for sending events, see [Using Resource\-Based Policies for CloudWatch Events](resource-based-policies-cwe.md)\.

## Which IAM condition keys can I use with CloudWatch Events<a name="SupportedAccessPolicies"></a>

CloudWatch Events supports the AWS\-wide condition keys \(see [Available Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/AvailableKeys.html) in the *IAM User Guide*\), plus the following service\-specific condition keys\. For more information, see [Using IAM Policy Conditions for Fine\-Grained Access Control](policy-keys-cwe.md)\.

## How can I tell when CloudWatch Events rules are broken<a name="CreateAlarmBrokenEventRules"></a>

You can use the following alarm to notify you when your CloudWatch Events rules are broken\.

**To create an alarm to alert when rules are broken**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Create Alarm**\. In the **CloudWatch Metrics by Category** pane, choose **Events Metrics**\.

1. In the list of metrics, select **FailedInvocations**\.

1. Above the graph, choose **Statistic**, **Sum**\.

1. For **Period**, choose a value, for example **5 minutes**\. Choose **Next**\.

1. Under **Alarm Threshold**, for **Name**, type a unique name for the alarm, for example **myFailedRules**\. For **Description**, type a description of the alarm, for example **Rules are not delivering events to targets**\.

1. For **is**, choose **>=** and **1**\. For **for**, enter **10**\.

1. Under **Actions**, for **Whenever this alarm**, choose **State is ALARM**\.

1. For **Send notification to**, select an existing Amazon SNS topic or create a new one\. To create a new topic, choose **New list**\. Type a name for the new Amazon SNS topic, for example: **myFailedRules**\. 

1. For **Email list**, type a comma\-separated list of email addresses to be notified when the alarm changes to the **ALARM** state\.

1. Choose **Create Alarm**\.
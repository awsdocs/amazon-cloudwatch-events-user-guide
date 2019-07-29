# Using Resource\-Based Policies for CloudWatch Events<a name="resource-based-policies-cwe"></a>

When a rule is triggered in CloudWatch Events, all the targets associated with the rule are invoked\. *Invocation* means invoking the AWS Lambda functions, publishing to the Amazon SNS topics, and relaying the event to the Kinesis streams\. In order to be able to make API calls against the resources you own, CloudWatch Events needs the appropriate permissions\. For Lambda, Amazon SNS, Amazon SQS, and CloudWatch Logs resources, CloudWatch Events relies on resource\-based policies\. For Kinesis streams, CloudWatch Events relies on IAM roles\.

You can use the following permissions to invoke the targets associated with your CloudWatch Events rules\. The following procedures use the AWS CLI to add permissions to your targets\. For information about how to install and configure the AWS CLI, see [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.

**Topics**
+ [AWS Lambda Permissions](#lambda-permissions)
+ [Amazon SNS Permissions](#sns-permissions)
+ [Amazon SQS Permissions](#sqs-permissions)
+ [CloudWatch Logs Permissions](#cloudwatchlogs-permissions)

## AWS Lambda Permissions<a name="lambda-permissions"></a>

To invoke your AWS Lambda function using a CloudWatch Events rule, add the following permission to the policy of your Lambda function\.

```
{
  "Effect": "Allow",
  "Action": "lambda:InvokeFunction",
  "Resource": "arn:aws:lambda:region:account-id:function:function-name",
  "Principal": {
    "Service": "events.amazonaws.com"
  },
  "Condition": {
    "ArnLike": {
      "AWS:SourceArn": "arn:aws:events:region:account-id:rule/rule-name"
    }
  },
  "Sid": "TrustCWEToInvokeMyLambdaFunction"
}
```

**To add permissions that enable CloudWatch Events to invoke Lambda functions**
+ At a command prompt, enter the following command:

  ```
  aws lambda add-permission --statement-id "TrustCWEToInvokeMyLambdaFunction" \
  --action "lambda:InvokeFunction" \
  --principal "events.amazonaws.com" \
  --function-name "arn:aws:lambda:region:account-id:function:function-name" \
  --source-arn "arn:aws:events:region:account-id:rule/rule-name"
  ```

For more information about setting permissions that enable CloudWatch Events to invoke Lambda functions, see [AddPermission](https://docs.aws.amazon.com/lambda/latest/dg/API_AddPermission.html) and [Using Lambda with Scheduled Events](https://docs.aws.amazon.com/lambda/latest/dg/with-scheduled-events.html) in the *AWS Lambda Developer Guide*\.

## Amazon SNS Permissions<a name="sns-permissions"></a>

To allow CloudWatch Events to publish an Amazon SNS topic, use the `aws sns get-topic-attributes` and the `aws sns set-topic-attributes` commands\.

**To add permissions that enable CloudWatch Events to publish SNS topics**

1. First, list SNS topic attributes\. At a command prompt, type the following:

   ```
   aws sns get-topic-attributes --topic-arn "arn:aws:sns:region:account-id:topic-name"
   ```

   The command returns all attributes of the SNS topic\. The following example shows the result of a newly created SNS topic\.

   ```
   {
       "Attributes": {
           "SubscriptionsConfirmed": "0", 
           "DisplayName": "", 
           "SubscriptionsDeleted": "0", 
           "EffectiveDeliveryPolicy": "{\"http\":{\"defaultHealthyRetryPolicy\":{\"minDelayTarget\":20,\"maxDelayTarget\":20,\"numRetries\":3,\"numMaxDelayRetries\":0,\"numNoDelayRetries\":0,\"numMinDelayRetries\":0,\"backoffFunction\":\"linear\"},\"disableSubscriptionOverrides\":false}}",
           "Owner": "account-id", 
           "Policy": "{\"Version\":\"2012-10-17\",\"Id\":\"__default_policy_ID\",\"Statement\":[{\"Sid\":\"__default_statement_ID\",\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"*\"},\"Action\":[\"SNS:GetTopicAttributes\",\"SNS:SetTopicAttributes\",\"SNS:AddPermission\",\"SNS:RemovePermission\",\"SNS:DeleteTopic\",\"SNS:Subscribe\",\"SNS:ListSubscriptionsByTopic\",\"SNS:Publish\",\"SNS:Receive\"],\"Resource\":\"arn:aws:sns:region:account-id:topic-name\",\"Condition\":{\"StringEquals\":{\"AWS:SourceOwner\":\"account-id\"}}}]}", 
           "TopicArn": "arn:aws:sns:region:account-id:topic-name", 
           "SubscriptionsPending": "0"
       }
   }
   ```

1. Next, convert the following statement to a string and add it to the "Statement" collection inside the "Policy" attribute\.

   ```
   {
     "Sid": "TrustCWEToPublishEventsToMyTopic",
     "Effect": "Allow",
     "Principal": {
       "Service": "events.amazonaws.com"
     },
     "Action": "sns:Publish",
     "Resource": "arn:aws:sns:region:account-id:topic-name"
   }
   ```

   After you convert the statement to a string, it should look like the following:

   ```
   {\"Sid\":\"TrustCWEToPublishEventsToMyTopic\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"events.amazonaws.com\"},\"Action\":\"sns:Publish\",\"Resource\":\"arn:aws:sns:region:account-id:topic-name\"}
   ```

1. After you've added the statement string to the statement collection, use the `aws sns set-topic-attributes` command to set the new policy\.

   ```
   aws sns set-topic-attributes --topic-arn "arn:aws:sns:region:account-id:topic-name" \
   --attribute-name Policy \
   --attribute-value "{\"Version\":\"2012-10-17\",\"Id\":\"__default_policy_ID\",\"Statement\":[{\"Sid\":\"__default_statement_ID\",\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"*\"},\"Action\":[\"SNS:GetTopicAttributes\",\"SNS:SetTopicAttributes\",\"SNS:AddPermission\",\"SNS:RemovePermission\",\"SNS:DeleteTopic\",\"SNS:Subscribe\",\"SNS:ListSubscriptionsByTopic\",\"SNS:Publish\",\"SNS:Receive\"],\"Resource\":\"arn:aws:sns:region:account-id:topic-name\",\"Condition\":{\"StringEquals\":{\"AWS:SourceOwner\":\"account-id\"}}}, {\"Sid\":\"TrustCWEToPublishEventsToMyTopic\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"events.amazonaws.com\"},\"Action\":\"sns:Publish\",\"Resource\":\"arn:aws:sns:region:account-id:topic-name\"}]}"
   ```

For more information, see the [SetTopicAttributes](https://docs.aws.amazon.com/sns/latest/api/API_SetTopicAttributes.html) action in the *Amazon Simple Notification Service API Reference*\.

## Amazon SQS Permissions<a name="sqs-permissions"></a>

To allow a CloudWatch Events rule to invoke an Amazon SQS queue, use the `aws sqs get-queue-attributes` and the `aws sqs set-queue-attributes` commands\.

**To add permissions that enable CloudWatch Events rules to invoke an SQS queue**

1. First, list SQS queue attributes\. At a command prompt, type the following:

   ```
   aws sqs get-queue-attributes \
   --queue-url https://sqs.region.amazonaws.com/account-id/queue-name \
   --attribute-names Policy
   ```

   For a newly created SQS queue, its policy is empty by default\. In addition to adding a statement, you also need to create a policy that contains this statement\.

1. The following statement enables CloudWatch Events to send messages to an SQS queue:

   ```
   {
     "Sid": "TrustCWEToSendEventsToMyQueue",
     "Effect": "Allow",
     "Principal": {
        "Service": "events.amazonaws.com"
     },
     "Action": "sqs:SendMessage",
     "Resource": "arn:aws:sqs:region:account-id:queue-name",
     "Condition": {
       "ArnEquals": {
         "aws:SourceArn": "arn:aws:events:region:account-id:rule/rule-name"
       }
     }
   }
   ```

1. Next, convert the preceding statement into a string\. After you convert the policy to a string, it should look like the following:

   ```
   {\"Sid\": \"TrustCWEToSendEventsToMyQueue\", \"Effect\": \"Allow\", \"Principal\": {\"Service\": \"events.amazonaws.com\"}, \"Action\": \"sqs:SendMessage\", \"Resource\": \"arn:aws:sqs:region:account-id:queue-name\", \"Condition\": {\"ArnEquals\": {\"aws:SourceArn\": \"arn:aws:events:region:account-id:rule/rule-name\"}}
   ```

1. Create a file called **set\-queue\-attributes\.json** with the following content:

   ```
   {
       "Policy": "{\"Version\":\"2012-10-17\",\"Id\":\"arn:aws:sqs:region:account-id:queue-name/SQSDefaultPolicy\",\"Statement\":[{\"Sid\": \"TrustCWEToSendEventsToMyQueue\", \"Effect\": \"Allow\", \"Principal\": {\"Service\": \"events.amazonaws.com\"}, \"Action\": \"sqs:SendMessage\", \"Resource\": \"arn:aws:sqs:region:account-id:queue-name\", \"Condition\": {\"ArnEquals\": {\"aws:SourceArn\": \"arn:aws:events:region:account-id:rule/rule-name\"}}}]}"
   }
   ```

1. Set the policy attribute using the set\-queue\-attributes\.json file as the input\. At a command prompt, type:

   ```
   aws sqs set-queue-attributes \
   --queue-url https://sqs.region.amazonaws.com/account-id/queue-name \
   --attributes file://set-queue-attributes.json
   ```

   If the SQS queue already has a policy, you need to copy the original policy and combine it with a new statement in the set\-queue\-attributes\.json file and run the preceding command to update the policy\.

For more information, see [Amazon SQS Policy Examples](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/SQSExamples.html) in the *Amazon Simple Queue Service Developer Guide*\.

## CloudWatch Logs Permissions<a name="cloudwatchlogs-permissions"></a>

When CloudWatch Logs is the target of a rule, CloudWatch Events creates log streams, and CloudWatch Logs stores the text from the triggering events as log entries\. To allow CloudWatch Events to create the log stream and log the events, CloudWatch Logs must include a resource\-based policy that enables CloudWatch Events to write to CloudWatch Logs\. If you use the AWS Management Console to add CloudWatch Logs as the target of a rule, this policy is created automatically\. If you use the AWS CLI to add the target, you must create this policy if it doesn't exist\. The following example shows the necessary policy\. This example allows CloudWatch Events to write to all log groups that have names that start with `/aws/events/`\. If you use a different log group naming policy for these types of logs, adjust the policy accordingly\.

```
{
    "Statement": [
        {
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Condition": {
                "ArnLike": {
                    "AWS:SourceArn": "arn:aws:events:{{region}}:{{account}}:*"
                }
            },
            "Effect": "Allow",
            "Principal": {
                "Service": "events.amazonaws.com"
            },
            "Resource": "arn:aws:logs:{{region}}:{{account}}:log-group:/aws/events/*:*",
            "Sid": "TrustEventsToStoreLogEvent"
        }
    ],
    "Version": "2012-10-17"
}
```

For more information, see [Controlling Access to Resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html#access_controlling-resources) in the *IAM User Guide*\.
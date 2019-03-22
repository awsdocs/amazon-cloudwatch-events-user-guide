# Using IAM Policy Conditions for Fine\-Grained Access Control<a name="policy-keys-cwe"></a>

When you grant permissions, you can use the IAM policy language to specify the conditions when a policy should take effect\. In a policy statement, you can optionally specify conditions that control when it is in effect\. Each condition contains one or more key\-value pairs\. Condition keys are not case\-sensitive\. For example, you might want a policy to be applied only after a specific date\.

If you specify multiple conditions, or multiple keys in a single condition, they are evaluated using a logical AND operation\. If you specify a single condition with multiple values for one key, they are evaluated using a logical OR operation\. For permission to be granted, all conditions must be met\. 

You can also use placeholders when you specify conditions\. For more information, see [Policy Variables](https://docs.aws.amazon.com/IAM/latest/UserGuide/policyvariables.html) in the *IAM User Guide*\. For more information about specifying conditions in an IAM policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_ElementDescriptions.html#Condition) in the *IAM User Guide*\.

By default, IAM users and roles can't access the events in your account\. To consume events, a user must be authorized for the `PutRule` API action\. If you allow an IAM user or role for the `events:PutRule` action in their policy, then they will be able to create a rule that matches certain events\. You must add a target to a rule, otherwise, a rule without a target does nothing except publish a CloudWatch metric when it matches an incoming event\. Your IAM user or role must have permissions for the `events:PutTargets` action\.

It is possible to limit access to the events by scoping the authorization to specific sources and types of events \(using the `events:source` and `events:detail-type` condition keys\)\. You can provide a condition in the policy statement of the IAM user or role that allows them to create a rule that only matches a specific set of sources and detail types\.

Similarly, through setting conditions in your policy statements, you can decide which specific resources in your accounts can be added to a rule by an IAM user or role \(using the `events:TargetArn` condition key\)\. For example, if you turn on CloudTrail in your account and you have a CloudTrail stream, CloudTrail events are also available to the users in your account through CloudWatch Events\. If you want your users to use CloudWatch Events and access all the events but the CloudTrail events, you can add a deny statement on the `PutRule` API action with a condition that any rule created by that user or role cannot match the CloudTrail event type\.

For CloudTrail events, you can limit the access to a specific principal that the original API call was originated from \(using the `events:detail.userIdentity.principalId` condition key\)\. For example, you can allow a user to see all the CloudTrail events, except the ones that are made by a specific IAM role in your account that you use for auditing or forensics\.


| Condition Key | Key/Value Pair | Evaluation Types | 
| --- | --- | --- | 
|  `events:source`  |  `"events:source":"source "` Where *source* is the literal string for the source field of the event such as `"aws.ec2"` and `"aws.s3"`\. To see more possible values for *source*, see the example events in [CloudWatch Events Event Examples From Supported Services](EventTypes.md)\.  |  Source, Null  | 
|  `events:detail-type`  |  `"events:detail-type":"detail-type "` Where *detail\-type* is the literal string for the **detail\-type** field of the event such as `"AWS API Call via CloudTrail"` and `"EC2 Instance State-change Notification"`\. To see more possible values for *detail\-type*, see the example events in [CloudWatch Events Event Examples From Supported Services](EventTypes.md)\.  |  Detail Type, Null  | 
|  `events: detail.userIdentity.principalId`  |  `"events:detail.userIdentity.principalId":"principal-id"` Where *principal\-id* is the literal string for the **detail\.userIdentity\.principalId** field of the event with detail\-type "AWS API Call via CloudTrail" such as `"AROAIDPPEZS35WEXAMPLE:AssumedRoleSessionName."`\.  |  Principal Id, Null  | 
|  `events: detail.service`  |  `"events:detail.service":"service"` Where *service* is the literal string for the **detail\.service** field of the event, such as `"ABUSE"`\.  |  service, Null  | 
|  `events: detail.eventTypeCode`  |  `"events:detail.eventTypeCode":"eventTypeCode"` Where *eventTypeCode* is the literal string for the **detail\.eventTypeCode** field of the event, such as `"AWS_ABUSE_DOS_REPORT"`\.  |  eventTypeCode, Null  | 
|  `events:TargetArn`  |  `"events:TargetArn":"target-arn "` Where *target\-arn* is the ARN of the target that can be put to a rule such as `"arn:aws:lambda:*:*:function:*"`\.  |  ARN, Null  | 

For example policy statements for CloudWatch Events, see [Overview of Managing Access Permissions to Your CloudWatch Events Resources](iam-access-control-identity-based-cwe.md)\.

**Topics**
+ [Example 1: Limit Access to a Specific Source](#EventsLimitAccessControl)
+ [Example 2: Define Multiple Sources That Can Be Used in an Event Pattern Individually](#EventsPatternSources)
+ [Example 3: Define a Source and a DetailType That Can Be Used in an Event Pattern](#EventsPatternDetailType)
+ [Example 4: Ensure That the Source Is Defined in the Event Pattern](#SourceDefinedEventsPattern)
+ [Example 5: Define a List of Allowed Sources in an Event Pattern with Multiple Sources](#AllowedSourcesEventsPattern)
+ [Example 6: Limiting PutRule Access by detail\.service](#LimitRuleByService)
+ [Example 7: Limiting PutRule Access by detail\.eventTypeCode](#LimitRuleByTypeCode)
+ [Example 8: Ensure That AWS CloudTrail Events for API Calls from a Certain PrincipalId Are Consumed](#ConsumeSpecificEvents)
+ [Example 9: Limiting Access to Targets](#LimitingAccessToTargets)

## Example 1: Limit Access to a Specific Source<a name="EventsLimitAccessControl"></a>

The following example policies can be attached to an IAM user\. Policy A allows the `PutRule` API action for all events, whereas Policy B allows `PutRule` only if the event pattern of the rule being created matches Amazon EC2 events\.

**Policy A:—allow any events** 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleForAllEvents",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*"
        }
    ]
    }
```

**Policy B:—allow events only from Amazon EC2** 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleForAllEC2Events",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:source": "aws.ec2"
                }
            }
        }
    ]
}
```

EventPattern is a mandatory argument to `PutRule`\. Hence, if the user with Policy B calls `PutRule` with an event pattern like the following:

```
{
    "source": [ "aws.ec2" ]
}
```

The rule would be created because the policy allows for this specific source, that is, "aws\.ec2"\. However, if the user with Policy B calls `PutRule` with an event pattern like the following:

```
{
    "source": [ "aws.s3" ]
}
```

The rule creation would be denied because the policy does not allow for this specific source, that is, "aws\.s3"\. Essentially, the user with Policy B is only allowed to create a rule that would match the events originating from Amazon EC2; hence, they are only allowed access to the events from Amazon EC2\.

See the following table for a comparison of Policy A and Policy B:


| Event Pattern | Allowed by Policy A | Allowed by Policy B | 
| --- | --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ]<br />}</pre>  |  Yes  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ec2", "aws.s3" ]<br />}</pre>  |  Yes  |  No \(Source aws\.s3 is not allowed\)  | 
|  <pre>{<br />    "source": [ "aws.ec2" ],<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  Yes  |  Yes  | 
|  <pre>{<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  Yes  |  No \(Source must be specified\)  | 

## Example 2: Define Multiple Sources That Can Be Used in an Event Pattern Individually<a name="EventsPatternSources"></a>

The following policy allows events from Amazon EC2 or CloudWatch Events\. In other words, it allows an IAM user or role to create a rule where the source in the EventPattern is specified as either "aws\.ec2" or "aws\.ecs"\. Not defining the source results in a "deny"\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleIfSourceIsEC2OrECS",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:source": [ "aws.ec2", "aws.ecs" ]
                }
            }
        }
    ]
}
```

See the following table for examples of event patterns that would be allowed or denied by this policy:


| Event Pattern | Allowed by the Policy | 
| --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ecs" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.s3" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "source": [ "aws.ec2", "aws.ecs" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "detail-type": [ "AWS API Call via CloudTrail" ]<br />}</pre>  |  No  | 

## Example 3: Define a Source and a DetailType That Can Be Used in an Event Pattern<a name="EventsPatternDetailType"></a>

The following policy allows events only from the `aws.ec2` source with DetailType equal to `EC2 instance state change notification`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleIfSourceIsEC2AndDetailTypeIsInstanceStateChangeNotification",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:source": "aws.ec2",
                    "events:detail-type": "EC2 Instance State-change Notification"
                }
            }
        }
    ]
}
```

See the following table for examples of event patterns that would be allowed or denied by this policy:


| Event Pattern | Allowed by the Policy | 
| --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "source": [ "aws.ecs" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "source": [ "aws.ec2" ],<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ec2" ],<br />    "detail-type": [ "EC2 Instance Health Failed" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  No  | 

## Example 4: Ensure That the Source Is Defined in the Event Pattern<a name="SourceDefinedEventsPattern"></a>

The following policy allows creating rules with `EventPatterns` that must have the source field\. In other words, an IAM user or role can't create a rule with an `EventPattern` that does not provide a specific source\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleIfSourceIsSpecified",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "Null": {
                    "events:source": "false"
                }
            }
        }
    ]
}
```

See the following table for examples of event patterns that would be allowed or denied by this policy:


| Event Pattern | Allowed by the Policy | 
| --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ],<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ecs", "aws.ec2" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  No  | 

## Example 5: Define a List of Allowed Sources in an Event Pattern with Multiple Sources<a name="AllowedSourcesEventsPattern"></a>

The following policy allows creating rules with `EventPatterns` that can have multiple sources in them\. Each source listed in the event pattern must be a member of the list provided in the condition\. When using the ForAllValues condition, make sure that at least one of the items in the condition list is defined\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleIfSourceIsSpecifiedAndIsEitherS3OrEC2OrBoth",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "ForAllValues:StringEquals": {
                    "events:source": [ "aws.ec2", "aws.s3" ]
                },
                "Null": {
                    "events:source": "false"
                }
            }
        }
    ]
}
```

See the following table for examples of event patterns that would be allowed or denied by this policy:


| Event Pattern | Allowed by the Policy | 
| --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ec2", "aws.s3" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ec2", "aws.autoscaling" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  No  | 

## Example 6: Limiting PutRule Access by detail\.service<a name="LimitRuleByService"></a>

You can restrict an IAM user or role to creating rules only for events that have a certain value in the `events:details.service` field\. The value of `events:details.service` is not necessarily the name of an AWS service\.

This policy condition is helpful when working with events from AWS Health that relate to security or abuse\. By using this policy condition, you can limit access to these sensitive alerts to only those users who need to see them\.

For example, the following policy allows the creation of rules only for events where the value of `events:details.service` is `ABUSE`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleEventsWithDetailServiceEC2",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:detail.service": "ABUSE"
                }
            }
        }
    ]
}
```

## Example 7: Limiting PutRule Access by detail\.eventTypeCode<a name="LimitRuleByTypeCode"></a>

You can restrict an IAM user or role to creating rules only for events that have a certain value in the `events:details.eventTypeCode` field\. This policy condition is helpful when working with events from AWS Health that relate to security or abuse\. By using this policy condition, you can limit access to these sensitive alerts to only those users who need to see them\.

 For example, the following policy allows the creation of rules only for events where the value of `events:details.eventTypeCode` is `AWS_ABUSE_DOS_REPORT`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleEventsWithDetailServiceEC2",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:detail.eventTypeCode": "AWS_ABUSE_DOS_REPORT"
                }
            }
        }
    ]
}
```

## Example 8: Ensure That AWS CloudTrail Events for API Calls from a Certain PrincipalId Are Consumed<a name="ConsumeSpecificEvents"></a>

All AWS CloudTrail events have the ID of the user who made the API call \(PrincipalId\) in the `detail.userIdentity.principalId` path of an event\. With the help of the `events:detail.userIdentity.principalId` condition key, you can limit the access of IAM users or roles to the CloudTrail events for only those coming from a specific account\.

```
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleOnlyForCloudTrailEventsWhereUserIsASpecificIAMUser",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:detail-type": [ "AWS API Call via CloudTrail" ],
                    "events:detail.userIdentity.principalId": [ "AIDAJ45Q7YFFAREXAMPLE" ]
                }
            }
        }
    ]
}
```

See the following table for examples of event patterns that would be allowed or denied by this policy:


| Event Pattern | Allowed by the Policy | 
| --- | --- | 
|  <pre>{<br />    "detail-type": [ "AWS API Call via CloudTrail" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "detail-type": [ "AWS API Call via CloudTrail" ],<br />    "detail.userIdentity.principalId": [ "AIDAJ45Q7YFFAREXAMPLE" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "detail-type": [ "AWS API Call via CloudTrail" ],<br />    "detail.userIdentity.principalId": [ "AROAIDPPEZS35WEXAMPLE:AssumedRoleSessionName" ]<br />}</pre>  |  No  | 

## Example 9: Limiting Access to Targets<a name="LimitingAccessToTargets"></a>

If an IAM user or role has `events:PutTargets` permission, they can add any target under the same account to the rules that they are allowed to access\. For example, the following policy limits adding targets to only a specific rule \(MyRule under account 123456789012\)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutTargetsOnASpecificRule",
            "Effect": "Allow",
            "Action": "events:PutTargets",
            "Resource": "arn:aws:events:us-east-1:123456789012:rule/MyRule"
        }
    ]
}
```

To limit what target can be added to the rule, use the `events:TargetArn` condition key\. For example, you can limit targets to only Lambda functions, as in the following example\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutTargetsOnASpecificRuleAndOnlyLambdaFunctions",
            "Effect": "Allow",
            "Action": "events:PutTargets",
            "Resource": "arn:aws:events:us-east-1:123456789012:rule/MyRule",
            "Condition": {
                "ArnLike": {
                    "events:TargetArn": "arn:aws:lambda:*:*:function:*"
                }
            }
        }
    ]
}
```
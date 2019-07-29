# CloudWatch Events Permissions Reference<a name="permissions-reference-cwe"></a>

When you are setting up [Access Control](auth-and-access-control-cwe.md#access-control-cwe) and writing permissions policies that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table lists each CloudWatch Events API operation and the corresponding actions for which you can grant permissions to perform the action\. You specify the actions in the policy's `Action` field, and you specify a wildcard character \(\*\) as the resource value in the policy's `Resource` field\.

You can use AWS\-wide condition keys in your CloudWatch Events policies to express conditions\. For a complete list of AWS\-wide keys, see [Available Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\.

**Note**  
To specify an action, use the `events:` prefix followed by the API operation name\. For example: `events:PutRule`, `events:EnableRule`, or `events:*` \(for all CloudWatch Events actions\)\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": ["events:action1", "events:action2"]
```

You can also specify multiple actions using wildcards\. For example, you can specify all actions whose name begins with the word "Put" as follows:

```
"Action": "events:Put*"
```

To specify all CloudWatch Events API actions, use the \* wildcard as follows:

```
"Action": "events:*"
```

The following table lists the actions that you can specify in an IAM policy for use with CloudWatch Events\.


**CloudWatch Events API Operations and Required Permissions for Actions**  

| CloudWatch Events API Operations | Required Permissions \(API Actions\) | 
| --- | --- | 
|  [DeleteRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DeleteRule.html)  |  `events:DeleteRule` Required to delete a rule\.  | 
|  [DescribeEventBus](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DescribeEventBus.html)  |  `events:DescribeEventBus` Required to list accounts that are allowed to write events to the current account's event bus\.  | 
|  [DescribeRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DescribeRule.html)  |  `events:DescribeRule` Required to list the details about a rule\.  | 
|  [DisableRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DisableRule.html)  |  `events:DisableRule` Required to disable a rule\.  | 
|  [EnableRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_EnableRule.html)  |  `events:EnableRule` Required to enable a rule\.  | 
|  [ListRuleNamesByTarget](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRuleNamesByTarget.html)  |  `events:ListRuleNamesByTarget` Required to list rules associated with a target\.  | 
|  [ListRules](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRules.html)  |  `events:ListRules` Required to list all rules in your account\.  | 
|  [ListTargetsByRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListTargetsByRule.html)  |  `events:ListTargetsByRule` Required to list all targets associated with a rule\.  | 
|  [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html)  |  `events:PutEvents` Required to add custom events that can be matched to rules\.  | 
|  [PutPermission](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutPermission.html)  |  `events:PutPermission` Required to give another account permission to write events to this account’s default event bus\.  | 
|  [PutRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutRule.html)  |  `events:PutRule` Required to create or update a rule\.  | 
|  [PutTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutTargets.html)  |  `events:PutTargets` Required to add targets to a rule\.  | 
|  [RemovePermission](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_RemovePermission.html)  |  `events:RemovePermission` Required to revoke another account’s permissions for writing events to this account’s default event bus\.  | 
|  [RemoveTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_RemoveTargets.html)  |  `events:RemoveTargets` Required to remove a target from a rule\.  | 
|  [TestEventPattern](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TestEventPattern.html)  |  `events:TestEventPattern` Required to test an event pattern against a given event\.  | 
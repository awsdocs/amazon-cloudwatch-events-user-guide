# CloudWatch Events Permissions Reference<a name="permissions-reference-cwe"></a>

When you are setting up [Access Control](auth-and-access-control-cwe.md#access-control-cwe) and writing permissions policies that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table lists each CloudWatch Events API operation and the corresponding actions for which you can grant permissions to perform the action\. You specify the actions in the policy's `Action` field, and you specify a wildcard character \(\*\) as the resource value in the policy's `Resource` field\.

You can use AWS\-wide condition keys in your CloudWatch Events policies to express conditions\. For a complete list of AWS\-wide keys, see [Available Keys](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\.

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

The actions you can specify in an IAM policy for use with CloudWatch Events are listed below\.


**CloudWatch Events API Operations and Required Permissions for Actions**  

| CloudWatch Events API Operations | Required Permissions \(API Actions\) | 
| --- | --- | 
|  [DeleteRule](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DeleteRule.html)  |  `events:DeleteRule` Required to delete a rule\.  | 
|  [DescribeRule](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DescribeRule.html)  |  `events:DescribeRule` Required to list the details about a rule\.  | 
|  [DisableRule](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DisableRule.html)  |  `events:DisableRule` Required to disable a rule\.  | 
|  [EnableRule](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_EnableRule.html)  |  `events:EnableRule` Required to enable a rule\.  | 
|  [ListRuleNamesByTarget](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRuleNamesByTarget.html)  |  `events:ListRuleNamesByTarget` Required to list rules associated with a target\.  | 
|  [ListRules](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRules.html)  |  `events:ListRules` Required to list all rules in your account\.  | 
|  [ListTargetsByRule](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListTargetsByRule.html)  |  `events:ListTargetsByRule` Required to list all targets associated with a rule\.  | 
|  [PutEvents](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html)  |  `events:PutEvents` Required to add custom events that can be matched to rules\.  | 
|  [PutRule](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutRule.html)  |  `events:PutRule` Required to create or update a rule\.  | 
|  [PutTargets](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutTargets.html)  |  `events:PutTargets` Required to add targets to a rule\.  | 
|  [RemoveTargets](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_RemoveTargets.html)  |  `events:RemoveTargets` Required to remove a target from a rule\.  | 
|  [TestEventPattern](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TestEventPattern.html)  |  `events:TestEventPattern` Required to test an event pattern against a given event\.  | 
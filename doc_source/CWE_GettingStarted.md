# Getting Started with Amazon CloudWatch Events<a name="CWE_GettingStarted"></a>

Use the procedures in this section to create and delete CloudWatch Events rules\. These are general procedures usable for any event source or target\. For tutorials written for specific scenarios and specific targets, see [Tutorials](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatch-Events-Tutorials.html)\.

**Topics**
+ [Creating a Rule That Triggers on an Event](Create-CloudWatch-Events-Rule.md)
+ [Creating a Rule That Triggers on an AWS API Call via CloudTrail](Create-CloudWatch-Events-CloudTrail-Rule.md)
+ [Creating a Rule That Triggers on a Schedule](Create-CloudWatch-Events-Scheduled-Rule.md)
+ [Deleting or Disabling a Rule](Delete-or-Disable-Rule.md)

**Limits**
+ Some target types might not be available in every region\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *Amazon Web Services General Reference*\.
+ Creating rules with built\-in targets is supported only in the AWS Management Console\.
+ If you create a rule with an encrypted Amazon SQS queue as a target, you must have the following section included in your KMS key policy\. It allows the event to be successfully delivered to the encrypted queue\.

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
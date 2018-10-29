# Logging Amazon CloudWatch Events API Calls with AWS CloudTrail<a name="logging_cw_api_calls_cwe"></a>

Amazon CloudWatch Events is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in CloudWatch Events\. CloudTrail captures API calls made by or on behalf of your AWS account\. The calls captured include calls from the CloudWatch console and code calls to the CloudWatch Events API operations\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for CloudWatch Events\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to CloudWatch Events, the IP address from which the request was made, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, including how to configure and enable it, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Topics**
+ [CloudWatch Events Information in CloudTrail](#cwe_info_in_ct)
+ [Example: CloudWatch Events Log File Entries](#understanding-CloudWatch-Events-entries-in-CloudTrail)

## CloudWatch Events Information in CloudTrail<a name="cwe_info_in_ct"></a>

CloudTrail is enabled on your AWS account when you create the account\. When supported event activity occurs in CloudWatch Events, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for CloudWatch Events, create a trail\. A *trail* enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all AWS Regions\. The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

CloudWatch Events supports logging the following actions as events in CloudTrail log files:
+ [DeleteRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DeleteRule.html)
+ [DescribeEventBus](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DescribeRule.html)
+ [DescribeRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DescribeRule.html)
+ [DisableRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DisableRule.html)
+ [EnableRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_EnableRule.html)
+ [ListRuleNamesByTarget](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRuleNamesByTarget.html)
+ [ListRules](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRules.html)
+ [ListTargetsByRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListTargetsByRule.html)
+ [PutPermission](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutPermission.html)
+ [PutRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutRule.html)
+ [PutTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutTargets.html)
+ [RemoveTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_RemoveTargets.html)
+ [TestEventPattern](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TestEventPattern.html)

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or AWS Identity and Access Management \(IAM\) user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

## Example: CloudWatch Events Log File Entries<a name="understanding-CloudWatch-Events-entries-in-CloudTrail"></a>

 A trail is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An event represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files aren't an ordered stack trace of the public API calls, so they don't appear in any specific order\.

The following CloudTrail log file entry shows that a user called the CloudWatch Events **PutRule** action\.

```
{
         "eventVersion":"1.03",
         "userIdentity":{
            "type":"Root",
            "principalId":"123456789012",
            "arn":"arn:aws:iam::123456789012:root",
            "accountId":"123456789012",
            "accessKeyId":"AKIAIOSFODNN7EXAMPLE",
            "sessionContext":{
               "attributes":{
                  "mfaAuthenticated":"false",
                  "creationDate":"2015-11-17T23:56:15Z"
               }
            }
         },
         "eventTime":"2015-11-18T00:11:28Z",
         "eventSource":"events.amazonaws.com",
         "eventName":"PutRule",
         "awsRegion":"us-east-1",
         "sourceIPAddress":"AWS Internal",
         "userAgent":"AWS CloudWatch Console",
         "requestParameters":{
            "description":"",
            "name":"cttest2",
            "state":"ENABLED",
            "eventPattern":"{\"source\":[\"aws.ec2\"],\"detail-type\":[\"EC2 Instance State-change Notification\"]}",
            "scheduleExpression":""
         },
         "responseElements":{
            "ruleArn":"arn:aws:events:us-east-1:123456789012:rule/cttest2"
         },
         "requestID":"e9caf887-8d88-11e5-a331-3332aa445952",
         "eventID":"49d14f36-6450-44a5-a501-b0fdcdfaeb98",
         "eventType":"AwsApiCall",
         "apiVersion":"2015-10-07",
         "recipientAccountId":"123456789012"
}
```
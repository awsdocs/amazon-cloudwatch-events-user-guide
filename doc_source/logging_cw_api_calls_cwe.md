# Logging Amazon CloudWatch Events API Calls in AWS CloudTrail<a name="logging_cw_api_calls_cwe"></a>

AWS CloudTrail is a service that captures API calls made by or on behalf of your AWS account\. This information is collected and written to log files that are stored in an Amazon S3 bucket that you specify\. API calls are logged whenever you use the API, the console, or the AWS CLI\. Using the information collected by CloudTrail, you can determine what request was made, the source IP address the request was made from, who made the request, when it was made, and so on\. 

To learn more about CloudTrail, including how to configure and enable it, see the [What is AWS CloudTrail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/what_is_cloud_trail_top_level.html) in the *AWS CloudTrail User Guide*\.

**Topics**
+ [CloudWatch Events Information in CloudTrail](#cw_info_in_ct_cwe)
+ [Understanding Log File Entries](#understanding_cw_log_file_entries_cwe)

## CloudWatch Events Information in CloudTrail<a name="cw_info_in_ct_cwe"></a>

If CloudTrail logging is turned on, calls made to API actions are captured in log files\. Every log file entry contains information about who generated the request\. For example, if a request is made to create a CloudWatch Events rule \(`PutRule`\), CloudTrail logs the identity of the person or service that made the request\.

The identity information in the log entry helps you determine the following:
+ Whether the request was made with root or IAM user credentials
+ Whether the request was made with temporary security credentials for a role or federated user
+ Whether the request was made by another AWS service

For more information, see the [CloudTrail userIdentity Element](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html) in the *AWS CloudTrail User Guide*\.

You can store your log files in your bucket for as long as you want, but you can also define Amazon S3 lifecycle rules to archive or delete log files automatically\. By default, your log files are encrypted by using Amazon S3 server\-side encryption \(SSE\)\.

To be notified upon log file delivery, you can configure CloudTrail to publish Amazon SNS notifications when new log files are delivered\. For more information, see [Configuring Amazon SNS Notifications for CloudTrail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html) in the *AWS CloudTrail User Guide*\.

You can also aggregate Amazon CloudWatch Logs log files from multiple AWS regions and multiple AWS accounts into a single S3 bucket\. For more information, see [Receiving CloudTrail Log Files from Multiple Regions](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html) and [Receiving CloudTrail Log Files from Multiple Accounts](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html) in the *AWS CloudTrail User Guide*\.

When logging is turned on, the following API actions are written to CloudTrail:
+ DeleteRule
+ DescribeRule
+ DisableRule
+ EnableRule
+ ListRuleNamesByTarget
+ ListRules
+ ListTargetsByRule
+ PutRule
+ PutTargets
+ RemoveTargets
+ TestEventPattern

For more information about these actions, see the [Amazon CloudWatch Events API Reference](http://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/)\.

## Understanding Log File Entries<a name="understanding_cw_log_file_entries_cwe"></a>

CloudTrail log files contain one or more log entries\. Each entry lists multiple JSON\-formatted events\. A log entry represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. The log entries are not an ordered stack trace of the public API calls, so they do not appear in any specific order\. Log file entries for all API actions are similar to the examples below\.

The following log file entry shows that a user called the CloudWatch Events **PutRule** action\.

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
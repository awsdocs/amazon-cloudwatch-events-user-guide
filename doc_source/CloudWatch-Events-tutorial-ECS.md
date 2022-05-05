# Tutorial: Run an Amazon ECS Task When a File is Uploaded to an Amazon S3 Bucket<a name="CloudWatch-Events-tutorial-ECS"></a>

You can use CloudWatch Events to run Amazon ECS tasks when certain AWS events occur\. In this tutorial, you set up a CloudWatch Events rule that runs an Amazon ECS task whenever a file is uploaded to a certain Amazon S3 bucket using the Amazon S3 PUT operation\.

This tutorial assumes that you have already created the task definition in Amazon ECS\.

### To run an Amazon ECS task whenever a file is uploaded to an S3 bucket using the PUT operation

**A) Create a CloudWatch Event Rule with ECS Task as a Target**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**\.

   1. Choose **Build event pattern to match events by service**\.

   1. For **Service Name**, choose **Simple Storage Service \(S3\)**\.

   1. For **Event Type**, choose **Object Level Operations**\.

   1. Choose **Specific operation\(s\)**, **Put Object**\.

   1. Choose **Specific bucket\(s\) by name**, and enter type the name of the bucket\.

1. For **Targets**, do the following:

   1. Choose **Add target**, **ECS task**\.

   1. For **Cluster** and **Task Definition**, select the resources that you created\.

   1. For **Launch Type**, choose `FARGATE` or `EC2`\. `FARGATE` is shown only in regions where AWS Fargate is supported\.

   1. \(Optional\) Specify a value for **Task Group**\. If the **Launch Type** is `FARGATE`, optionally specify a **Platform Version**\. Specify only the numeric portion of the platform version, such as 1\.1\.0\.

   1. \(Optional\) specify a task definition revision and task count\. If you do not specify a task definition revision, the latest is used\.

   1. If your task definition uses the awsvpc network mode, you must specify subnets and security groups\. All subnets and security groups must be in the same VPC\.

      If you specify more than one security group or subnet, separate them with commas but not spaces\.

      For **Subnets**, specify the entire `subnet-id` value for each subnet, as in the following example:

      `subnet-123abcd,subnet-789abcd`

   1. Choose whether to allow the public IP address to be auto\-assigned\.

   1. CloudWatch Events can create the IAM role needed for your task to run: 
      + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
      + To use an IAM role that you created before, choose **Use existing role**\. This must be a role that already has sufficient permissions to invoke the build\. CloudWatch Events does not grant additional permissions to the role that you select\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for the rule\.

1. Choose **Create rule**\.

**B) Enable CloudTrail event logging for S3 buckets for `S3:PutObjects` API call**

You can use the Amazon S3 console to configure an AWS CloudTrail trail to log data events for objects in an S3 bucket. CloudTrail supports logging Amazon S3 object-level API operations such as `GetObject`, `DeleteObject`, and `PutObject`. These events are called data events.

By default, CloudTrail trails don't log data events, but you can configure trails to log data events for S3 buckets that you specify, or to log data events for all the Amazon S3 buckets in your AWS account.

To configure a trail to log data events for an S3 bucket, you can use either the AWS CloudTrail console or the Amazon S3 console. If you are configuring a trail to log data events for all the Amazon S3 buckets in your AWS account, it's easier to use the CloudTrail console.

**The following procedure shows how to use the Amazon S3 console to configure a CloudTrail trail to log data events for an S3 bucket.**

> To enable CloudTrail data events logging for objects in an S3 bucket

1. Sign in to the AWS Management Console and open the Amazon S3 console at https://console.aws.amazon.com/s3/.

2. In the **Buckets** list, choose the name of the bucket.

3. Choose **Properties**.

4. Under **AWS CloudTrail data events**, choose **Configure in CloudTrail**.
You can create a new CloudTrail trail or reuse an existing trail and configure Amazon S3 data events to be logged in your trail. For information about how to create trails in the CloudTrail console, see [Creating and updating a trail with the console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events) in the AWS CloudTrail User Guide. For information about how to configure Amazon S3 data event logging in the CloudTrail console, see [Logging data events for Amazon S3 Objects](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events-examples) in the AWS CloudTrail User Guide.

**Note:**
If you use the CloudTrail console or the Amazon S3 console to configure a trail to log data events for an S3 bucket, the Amazon S3 console shows that object-level logging is enabled for the bucket.

**To disable CloudTrail data events logging for objects in an S3 bucket**

1. To disable object-level logging for the bucket, you must open the CloudTrail console and remove the bucket name from the trail's Data events.

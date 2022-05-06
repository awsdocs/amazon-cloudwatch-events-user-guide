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

**To enable CloudTrail data events logging for objects in an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at https://console.aws.amazon.com/s3/.

2. In the **Buckets** list, choose the name of the bucket.

3. Choose **Properties**.

4. Under **AWS CloudTrail data events**, choose **Configure in CloudTrail**.
You can create a new CloudTrail trail or reuse an existing trail and configure Amazon S3 data events to be logged in your trail. 

5. This will open **Trails** page in AWS CloudTrail console and then choose the trail name\.
*****Note**: While you can edit an existing trail to add logging data events, as a best practice, consider creating a separate trail specifically for logging data events\.***

6. To create a new CloudTrail trail with the AWS Management Console follow below steps:
   1. Choose **Create trail**\.

   2. On the **Create Trail** page, for **Trail name**, type a name for your trail\. For more information, see [CloudTrail trail naming requirements](cloudtrail-trail-naming-requirements.md)\.

   3. For **Storage location**, choose **Create new S3 bucket** to create a bucket\. When you create a bucket, CloudTrail creates and applies the required bucket policies\.
**Note**  If you chose **Use existing S3 bucket**, specify a bucket in **Trail log bucket name**, or choose **Browse** to choose a bucket\. The bucket policy must grant CloudTrail permission to write to it\. For information about manually editing the bucket policy, see [Amazon S3 bucket policy for CloudTrail](create-s3-bucket-policy-for-cloudtrail.md)\.

   To make it easier to find your logs, create a new folder \(also known as a *prefix*\) in an existing bucket to store your CloudTrail logs\. Enter the prefix in **Prefix**\.

   4. For **Log file SSE\-KMS encryption**, choose **Disabled** for this lab, however if you want to encrypt your log files with SSE\-KMS instead of SSE\-S3\. Choose **Enabled**\. For more information about this encryption type, see [Protecting Data Using Server\-Side Encryption with Amazon S3\-Managed Encryption Keys \(SSE\-S3\)](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html)\.

   5. In **Additional settings**, configure the following\.

      1. For **Log file validation**, choose **Enabled** to have log digests delivered to your S3 bucket\. You can use the digest files to verify that your log files did not change after CloudTrail delivered them\. For more information, see [Validating CloudTrail log file integrity](cloudtrail-log-file-validation-intro.md)\.

      2. Clink **Next** and follow below Step 8.

8. If you already have a **Trail** Click on it and Choose **Edit** under **Data events** 
OR 
If are creating a new trail using above steps 6, then choose the For **Data events**, choose **Edit**\.

9. For Amazon S3 buckets:

   1. For **Data event source**, choose **S3**\.

   2. You can choose to log **All current and future S3 buckets**, or you can specify individual buckets or functions\. By default, data events are logged for all current and future S3 buckets\.
**Note**  
Keeping the default **All current and future S3 buckets** option enables data event logging for all buckets currently in your AWS account and any buckets you create after you finish creating the trail\. It also enables logging of data event activity performed by any user or role in your AWS account, even if that activity is performed on a bucket that belongs to another AWS account\.  
If you are creating a trail for a single Region \(done by using the AWS CLI\), selecting the **Select all S3 buckets in your account** option enables data event logging for all buckets in the same Region as your trail and any buckets you create later in that Region\. It will not log data events for Amazon S3 buckets in other Regions in your AWS account\.

   3. If you leave the default, **All current and future S3 buckets**, choose to log **Read** events, **Write** events, or both\.

   4. To select individual buckets, empty the **Read** and **Write** check boxes for **All current and future S3 buckets**\. In **Individual bucket selection**, browse for a bucket on which to log data events\. To find specific buckets, type a bucket prefix for the bucket you want\. You can select multiple buckets in this window\. Choose **Add bucket** to log data events for more buckets\. Choose to log **Read** events, such as `GetObject`, **Write** events, such as `PutObject`, or both\.

      This setting takes precedence over individual settings you configure for individual buckets\. For example, if you specify logging **Read** events for all S3 buckets, and then choose to add a specific bucket for data event logging, **Read** is already selected for the bucket you added\. You cannot clear the selection\. You can only configure the option for **Write**\.

      To remove a bucket from logging, choose **X**\.

   5. To add another data type on which to log data events, choose **Add data event type**\.


* For information about how to create and update trails in the CloudTrail console, see [Creating a trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) [Updating a trail with the console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events) in the AWS CloudTrail User Guide. 
* For information about how to configure Amazon S3 data event logging in the CloudTrail console, see [Logging data events for Amazon S3 Objects](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events-examples) in the AWS CloudTrail User Guide.

**Note:**
If object-level logging is successfully enabled via CloudTrail console or the Amazon S3 console to configure a trail to log data events for an S3 bucket, the Amazon S3 console shows that object-level logging is enabled for the bucket.

**To disable CloudTrail data events logging for objects in an S3 bucket**

1. To disable object-level logging for the bucket, you must open the CloudTrail console and remove the bucket name from the trail's Data events.

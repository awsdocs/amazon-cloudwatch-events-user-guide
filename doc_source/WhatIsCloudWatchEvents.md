# What Is Amazon CloudWatch Events?<a name="WhatIsCloudWatchEvents"></a>

Amazon CloudWatch Events delivers a near real\-time stream of system events that describe changes in Amazon Web Services \(AWS\) resources\. Using simple rules that you can quickly set up, you can match events and route them to one or more target functions or streams\. CloudWatch Events becomes aware of operational changes as they occur\. CloudWatch Events responds to these operational changes and takes corrective action as necessary, by sending messages to respond to the environment, activating functions, making changes, and capturing state information\.

You can also use CloudWatch Events to schedule automated actions that self\-trigger at certain times using cron or rate expressions\. For more information, see [Schedule Expressions for Rules](ScheduledEvents.md)\.

You can configure the following AWS services as targets for CloudWatch Events:
+ Amazon EC2 instances
+ AWS Lambda functions
+ Streams in Amazon Kinesis Data Streams
+ Delivery streams in Amazon Kinesis Data Firehose
+ Log groups in Amazon CloudWatch Logs
+ Amazon ECS tasks
+ Systems Manager Run Command
+ Systems Manager Automation
+ AWS Batch jobs
+ Step Functions state machines
+ Pipelines in CodePipeline
+ CodeBuild projects
+ Amazon Inspector assessment templates
+ Amazon SNS topics
+ Amazon SQS queues
+ Built\-in targets: `EC2 CreateSnapshot API call`, `EC2 RebootInstances API call`, `EC2 StopInstances API call`, and `EC2 TerminateInstances API call`\.
+ The default event bus of another AWS account

## Concepts<a name="CloudWatchEventsComponents"></a>

Before you begin using CloudWatch Events, you should understand the following concepts:
+ **Events** – An event indicates a change in your AWS environment\. AWS resources can generate events when their state changes\. For example, Amazon EC2 generates an event when the state of an EC2 instance changes from pending to running, and Amazon EC2 Auto Scaling generates events when it launches or terminates instances\. AWS CloudTrail publishes events when you make API calls\. You can generate custom application\-level events and publish them to CloudWatch Events\. You can also set up scheduled events that are generated on a periodic basis\. For a list of services that generate events, and sample events from each service, see [CloudWatch Events Event Examples From Supported Services](EventTypes.md)\.
+ **Targets** – A target processes events\. Targets can include Amazon EC2 instances, AWS Lambda functions, Kinesis streams, Amazon ECS tasks, Step Functions state machines, Amazon SNS topics, Amazon SQS queues, and built\-in targets\. A target receives events in JSON format\.
+ **Rules** – A rule matches incoming events and routes them to targets for processing\. A single rule can route to multiple targets, all of which are processed in parallel\. Rules are not processed in a particular order\. This enables different parts of an organization to look for and process the events that are of interest to them\. A rule can customize the JSON sent to the target, by passing only certain parts or by overwriting it with a constant\.

## Related AWS Services<a name="related_services_cwe"></a>

The following services are used in conjunction with CloudWatch Events:
+ **AWS CloudTrail** enables you to monitor the calls made to the CloudWatch Events API for your account, including calls made by the AWS Management Console, the AWS CLI and other services\. When CloudTrail logging is turned on, CloudWatch Events writes log files to an S3 bucket\. Each log file contains one or more records, depending on how many actions are performed to satisfy a request\. For more information, see [Logging Amazon CloudWatch Events API Calls with AWS CloudTrail](logging_cw_api_calls_cwe.md)\.
+ **AWS CloudFormation** enables you to model and set up your AWS resources\. You create a template that describes the AWS resources you want, and AWS CloudFormation takes care of provisioning and configuring those resources for you\. You can use CloudWatch Events rules in your AWS CloudFormation templates\. For more information, see [AWS::Events::Rule](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-events-rule.html) in the AWS CloudFormation User Guide\.
+ **AWS Config** enables you to record configuration changes to your AWS resources\. This includes how resources relate to one another and how they were configured in the past, so that you can see how the configurations and relationships change over time\. You can also create AWS Config rules to check whether your resources are compliant or noncompliant with your organization's policies\. For more information, see the [AWS Config Developer Guide](https://docs.aws.amazon.com/config/latest/developerguide/)\.
+ **AWS Identity and Access Management \(IAM\)** helps you securely control access to AWS resources for your users\. Use IAM to control who can use your AWS resources \(authentication\), what resources they can use, and how they can use them \(authorization\)\. For more information, see [Authentication and Access Control for Amazon CloudWatch Events](auth-and-access-control-cwe.md)\.
+ **Amazon Kinesis Data Streams** enables rapid and nearly continuous data intake and aggregation\. The type of data used includes IT infrastructure log data, application logs, social media, market data feeds, and web clickstream data\. Because the response time for the data intake and processing is in real time, processing is typically lightweight\. For more information, see the [Amazon Kinesis Data Streams Developer Guide](https://docs.aws.amazon.com/streams/latest/dev/)\.
+ **AWS Lambda** enables you to build applications that respond quickly to new information\. Upload your application code as Lambda functions and Lambda runs your code on high\-availability compute infrastructure\. Lambda performs all the administration of the compute resources, including server and operating system maintenance, capacity provisioning, automatic scaling, code and security patch deployment, and code monitoring and logging\. For more information, see the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)\.
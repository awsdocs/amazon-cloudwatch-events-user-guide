# Tutorial: Use CloudWatch Events to Relay Events to Amazon EC2 Run Command<a name="EC2_Run_Command"></a>

You can use Amazon CloudWatch Events to invoke AWS Systems Manager Run Command and perform actions on Amazon EC2 instances when certain events happen\. In this tutorial, set up Run Command to run shell commands and configure each new instance that is launched in an Amazon EC2 Auto Scaling group\. This tutorial assumes that you have already assigned a tag to the Amazon EC2 Auto Scaling group, with `environment` as the key and `production` as the value\.

**To create the CloudWatch Events rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**, **Build event pattern to match events by service**\.

   1. For **Service Name**, choose **Auto Scaling**\. For **Event Type**, choose **Instance Launch and Terminate**\.

   1. Choose **Specific instance event\(s\)**, **EC2 Instance\-launch Lifecycle Action**\.

   1. By default, the rule matches any Amazon EC2 Auto Scaling group in the region\. To make the rule match a specific group, choose **Specific group name\(s\)** and then select one or more groups\.  
![\[The Event Source pane\]](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/images/cwe_tutorial_runcmd1.PNG)

1. For **Targets**, choose **Add Target**, **SSM Run Command\.** 

1. For **Document**, choose **AWS\-RunShellScript \(Linux\)**\. There are many other **Document** options that cover both Linux and Windows instances\. For **Target key**, type **tag:environment**\. For **Target value\(s\)**, type **production** and choose **Add**\.

1. Under **Configure parameter\(s\)**, choose **Constant**\.

1. For **Commands**, type a shell command and choose **Add**\. Repeat this step for all commands to run when an instance launches\. 

1. If necessary, type the appropriate information in **WorkingDirectory** and **ExecutionTimeout**\.

1. CloudWatch Events can create the IAM role needed for your event to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created before, choose **Use existing role**\.  
![\[The Target selector pane\]](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/images/cwe_tutorial_runcmd2.PNG)

1. Choose **Configure details**\. For **Rule definition**, type a name and description for the rule\. 

1. Choose **Create rule**\.
# Using CloudWatch Events with Interface VPC Endpoints<a name="cloudwatch-events-and-interface-VPC"></a>

If you use Amazon Virtual Private Cloud \(Amazon VPC\) to host your AWS resources, you can establish a private connection between your VPC and CloudWatch Events\. You can use this connection to enable CloudWatch Events to communicate with your resources on your VPC without going through the public internet\.

Amazon VPC is an AWS service that you can use to launch AWS resources in a virtual network that you define\. With a VPC, you have control over your network settings, such the IP address range, subnets, route tables, and network gateways\. To connect your VPC to CloudWatch Events, you define an *interface VPC endpoint* for CloudWatch Events\. This type of endpoint enables you to connect your VPC to AWS services\. The endpoint provides reliable, scalable connectivity to CloudWatch Events without requiring an internet gateway, network address translation \(NAT\) instance, or VPN connection\. For more information, see [What is Amazon VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/) in the *Amazon VPC User Guide*\.

 Interface VPC endpoints are powered by AWS PrivateLink, an AWS technology that enables private communication between AWS services using an elastic network interface with private IP addresses\. For more information, see [New – AWS PrivateLink for AWS Services](https://aws.amazon.com/blogs/aws/new-aws-privatelink-endpoints-kinesis-ec2-systems-manager-and-elb-apis-in-your-vpc/)\.

The following steps are for users of Amazon VPC\. For more information, see [Getting Started](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/GetStarted.html) in the *Amazon VPC User Guide*\.

## Availability<a name="cloudwatch-events-interface-VPC-availability"></a>

CloudWatch Events currently supports VPC endpoints in the following Regions:
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ EU \(Frankfurt\)
+ EU \(Ireland\)
+ EU \(London\)
+ EU \(Paris\)
+ South America \(São Paulo\)

## Create a VPC Endpoint for CloudWatch Events<a name="create-VPC-endpoint-for-CloudWatchEvents"></a>

To start using CloudWatch Events with your VPC, create an interface VPC endpoint for CloudWatch Events\. For more information, see [Creating an Interface Endpoint](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpce-interface.html#create-interface-endpoint.html) in the *Amazon VPC User Guide*\.

You do not need to change the settings for CloudWatch Events\. CloudWatch Events calls other AWS services using either public endpoints or private interface VPC endpoints, whichever are in use\. For example, if you create an interface VPC endpoint for CloudWatch Events, and you already have a CloudWatch Events rule that sends notifications to Amazon SNS when it is triggered, the notifications begin to flow through the interface VPC endpoint\.
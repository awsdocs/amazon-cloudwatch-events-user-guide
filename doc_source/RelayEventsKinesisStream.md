# Tutorial: Relay Events to an Amazon Kinesis Stream Using CloudWatch Events<a name="RelayEventsKinesisStream"></a>

You can relay AWS API call events in CloudWatch Events to a stream in Amazon Kinesis\.

## Prerequisite<a name="stream-prerequisite"></a>

Install the AWS CLI\. For more information, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

## Step 1: Create an Amazon Kinesis Stream<a name="stream-create-stream"></a>

Use the following [create\-stream](https://docs.aws.amazon.com/cli/latest/reference/kinesis/create-stream.html) command to create a stream\.

```
aws kinesis create-stream --stream-name test --shard-count 1
```

When the stream status is `ACTIVE`, the stream is ready\. Use the following [describe\-stream](https://docs.aws.amazon.com/cli/latest/reference/kinesis/describe-stream.html) command to check the stream status:

```
aws kinesis describe-stream --stream-name test
```

## Step 2: Create a Rule<a name="stream-create-rule"></a>

As an example, create a rule to send events to your stream when you stop an Amazon EC2 instance\.

**To create a rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**\.

   1. Choose **Build event pattern to match events by service**\.

   1. Choose **EC2**, **Instance State\-change Notification**\.

   1. Choose **Specific state\(s\)**, **Running**\.

1. For **Targets**, choose **Add target**, **Kinesis stream**\.

1. For **Stream**, select the stream that you created\.

1. Choose **Create a new role for this specific resource**\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for the rule\.

1. Choose **Create rule**\.

## Step 3: Test the Rule<a name="stream-test-rule"></a>

To test your rule, stop an Amazon EC2 instance\. After waiting a few minutes for the instance to stop, check your CloudWatch metrics to verify that your function was invoked\.

**To test your rule by stopping an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Launch an instance\. For more information, see [Launch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Rules**, select the name of the rule that you created, and choose **Show metrics for the rule**\.

1. \(Optional\) When you are finished, you can terminate the instance\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Step 4: Verify That the Event is Relayed<a name="stream-verify-event"></a>

You can get the record from the stream to verify that the event was relayed\.

**To get the record**

1. Use the following [get\-shard\-iterator](https://docs.aws.amazon.com/cli/latest/reference/kinesis/get-shard-iterator.html) command to start reading from your Kinesis stream:

   ```
   aws kinesis get-shard-iterator --shard-id shardId-000000000000 --shard-iterator-type TRIM_HORIZON --stream-name test
   ```

   The following is example output:

   ```
   {
       "ShardIterator": "AAAAAAAAAAHSywljv0zEgPX4NyKdZ5wryMzP9yALs8NeKbUjp1IxtZs1Sp+KEd9I6AJ9ZG4lNR1EMi+9Md/nHvtLyxpfhEzYvkTZ4D9DQVz/mBYWRO6OTZRKnW9gd+efGN2aHFdkH1rJl4BL9Wyrk+ghYG22D2T1Da2EyNSH1+LAbK33gQweTJADBdyMwlo5r6PqcP2dzhg="
   }
   ```

1. Use the following get\-records command to get the record\. The shard iterator is the one you got in the previous step:

   ```
   aws kinesis get-records --shard-iterator AAAAAAAAAAHSywljv0zEgPX4NyKdZ5wryMzP9yALs8NeKbUjp1IxtZs1Sp+KEd9I6AJ9ZG4lNR1EMi+9Md/nHvtLyxpfhEzYvkTZ4D9DQVz/mBYWRO6OTZRKnW9gd+efGN2aHFdkH1rJl4BL9Wyrk+ghYG22D2T1Da2EyNSH1+LAbK33gQweTJADBdyMwlo5r6PqcP2dzhg=
   ```

   If the command is successful, it requests records from your stream for the specified shard\. You can receive zero or more records\. Any records returned might not represent all records in your stream\. If you don't receive the data you expect, keep calling `get-records`\.

   Records in Kinesis are Base64\-encoded\. However, the streams support in the AWS CLI does not provide base64 decoding\. If you use a base64 decoder to manually decode the data, you see that it is the event relayed to the stream in JSON form\.
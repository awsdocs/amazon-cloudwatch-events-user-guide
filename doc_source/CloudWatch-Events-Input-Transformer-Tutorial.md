# Tutorial: Use Input Transformer to Customize What is Passed to the Event Target<a name="CloudWatch-Events-Input-Transformer-Tutorial"></a>

You can use the input transformer feature of CloudWatch Events to customize the text that is taken from an event before it is input to the target of a rule\. 

You can define multiple JSON paths from the event and assign their outputs to different variables\. Then you can use those variables in the input template as <*variable\-name*>\. The characters < and > cannot be escaped\.

If you specify a variable to match a JSON path that does not exist in the event, then that variable is not created and does not appear in the output\.

In this tutorial, we extract the instance\-id and state of an Amazon EC2 instance from the instance state change event\. We use the input transformer to put that data into an easy\-to\-read message that is sent to an Amazon SNS topic\. The rule is triggered when any instance changes to any state\. For example, with this rule, the following Amazon EC2 instance state\-change notification event produces the Amazon SNS message **The EC2 instance i\-1234567890abcdef0 has changed state to stopped\.**

```
{
   "id":"7bf73129-1428-4cd3-a780-95db273d1602",
   "detail-type":"EC2 Instance State-change Notification",
   "source":"aws.ec2",
   "account":"123456789012",
   "time":"2015-11-11T21:29:54Z",
   "region":"us-east-1",
   "resources":[
      "arn:aws:ec2:us-east-1:123456789012:instance/ i-1234567890abcdef0"
   ],
   "detail":{
      "instance-id":" i-1234567890abcdef0",
      "state":"stopped"
   }
}
```

We achieve this by mapping the *instance* variable to the `$.detail.instance-id` JSON path from the event, and the *state* variable to the `$.detail.state` JSON path\. We then set the input template as "The EC2 instance <instance> has changed state to <state>\."

## Create a Rule<a name="input-transformer-create-rule"></a>

**To customize instance state change information sent to a target using the input transformer**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, do the following:

   1. Choose **Event Pattern**\.

   1. Choose **Build event pattern to match events by service**\.

   1. Choose **EC2**, **EC2 Instance State\-change Notification**\.

   1. Choose **Any state**, **Any instance**\.

1. For **Targets**, choose **Add target**, **SNS topic**\.

1. For **Topic**, select the Amazon SNS topic for which to be notified when Amazon EC2 instances change state\.

1. Choose **Configure input**, **Input Transformer**\.

1. In the next box, type **\{"state" : "$\.detail\.state", "instance" : "$\.detail\.instance\-id"\}**

1. In the following box, type **"The EC2 instance <instance> has changed state to <state>\."**

1. Choose **Configure details**\.

1. Type a name and description for the rule, and choose **Create rule**\.
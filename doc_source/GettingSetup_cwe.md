# Setting Up Amazon CloudWatch Events<a name="GettingSetup_cwe"></a>

To use Amazon CloudWatch Events you need an AWS account\. Your AWS account allows you to use services \(for example, Amazon EC2\) to generate events that you can view in the CloudWatch console, a web\-based interface\. In addition, you can install and configure the AWS Command Line Interface \(AWS CLI\) to use a command\-line interface\.

## Sign Up for Amazon Web Services \(AWS\)<a name="signup_cwe"></a>

When you create an AWS account, we automatically sign up your account for all AWS services\. You pay only for the services that you use\. 

If you have an AWS account already, skip to the next step\. If you don't have an AWS account, use the following procedure to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Sign in to the Amazon CloudWatch Console<a name="ConsoleSignIn_cwe"></a>

**To sign in to the Amazon CloudWatch console**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the region\. From the navigation bar, choose the region where you have your AWS resources\.

1. In the navigation pane, choose **Events**\.

## Account Credentials<a name="manage-account-credentials"></a>

Although you can use your root user credentials to access CloudWatch Events, we recommend that you use an AWS Identity and Access Management \(IAM\) account\. If you're using an IAM account to access CloudWatch, you must have the following permissions:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "events:*",
        "iam:PassRole"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

For more information, see [Authentication](auth-and-access-control-cwe.md#authentication-cwe)\.

## Set Up the Command Line Interface<a name="SetupCLI_cwe"></a>

You can use the AWS CLI to perform CloudWatch Events operations\.

For information about how to install and configure the AWS CLI, see [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.

## Regional Endpoints<a name="CWE_Prerequisites"></a>

You must enable regional endpoints \(the default\) in order to use CloudWatch Events\. For more information, see [Activating and Deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *IAM User Guide*\.
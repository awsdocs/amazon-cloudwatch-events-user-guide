# Tutorial: Schedule Automated Builds Using CodeBuild<a name="CloudWatch-Events-tutorial-codebuild"></a>

In the example in this tutorial, you schedule CodeBuild to run a build every week night at 8PM GMT\. You also pass a constant to CodeBuild to be used for this scheduled build\.

**To create a rule scheduling an CodeBuild project build nightly at 8PM**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. For **Event Source**, do the following:

   1. Choose **Schedule**\.

   1. Choose **Cron expression** and specify the following as the expression: **0 20 ? \* MON\-FRI \***\. For more information about cron expressions, see [Schedule Expressions for Rules](ScheduledEvents.md)\.

1. For **Targets**, choose **Add target**, **CodeBuild project**\.

1. For **Project ARN**, type the ARN of the build project\.

1. In this tutorial, we add the optional step of passing a parameter to CodeBuild, to override the default\. This is not required when you set CodeBuild as the target\. To pass the parameter, choose **Configure input**, **Constant \(JSON text\)**\.

   In the box under **Constant \(JSON text\)**, type the following to set the timeout override to 30 minutes for these scheduled builds: **\{ "timeoutInMinutesOverride": 30 \}**

   For more information about the parameters you can pass, see [StartBuild](https://docs.aws.amazon.com/codebuild/latest/APIReference/API_StartBuild.html)\. You cannot pass the `projectName` parameter in this field\. Instead, you specify the project using the ARN in **Project ARN**\.

1. CloudWatch Events can create the IAM role needed for your build project to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created before, choose **Use existing role**\. This must be a role that already has sufficient permissions to invoke the build\. CloudWatch Events does not grant additional permissions to the role that you select\.

1. Choose **Configure details**

1. For **Rule definition**, type a name and description for the rule\.

1. Choose **Create rule**\.
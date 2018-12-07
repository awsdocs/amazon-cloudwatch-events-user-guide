# Amazon CloudWatch Events Managed Rules<a name="CloudWatch-Events-Managed-Rules"></a>

Other AWS services can create and manage CloudWatch Events rules in your AWS account that are needed for certain functions in those services\. These are called *managed rules*\. 

When a service creates a managed rule, it may also create an IAM policy that grants permissions to that service to create the rule\. IAM policies created this way are scoped narrowly with resource\-level permissions, to allow the creation of only the necessary rules\.

You can delete managed rules by using the **Force delete** option\. Do so only if you are sure that the other service no longer needs the rule\. Otherwise, deleting a managed rule causes the features that rely on it to stop working\.
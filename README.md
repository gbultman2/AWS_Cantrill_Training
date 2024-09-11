# AWS_Cantrill_Training
Project Animals4Life through Adrian Cantrill's course.

# Setting Up the Organization
AWS has organizations that can control accounts.  You have a management/master account and have organizational accounts.  It's best to structure the organizational accounts inside of organizational units.  This allows you to apply security control policies to the organizational unit which will then be applied to each account in that group.  

For this project, we set up a general account, a prod account within a prod OU and a dev account within a dev OU.  We didn't use an SCP, but I have one in the diagram just to show where they go and what they apply to.  The SCP can be set up to restrict access to certain products within AWS.  For instance, we could set it up to deny access to S3 and accounts within the OU would not be able to access S3.

![organization](organization.jpg)

## Cloud Trail and Cloud Watch Logs
We've enabled a cloud trail across all of the accounts in the organization.  It will record API events for management actions on the account.  This includes things like changing IAM policies, creating EC2 instances, creating S3 buckets etc.  We set it up to monitor events in all AWS regions.  Data events and insights events are disabled as they will likely incur costs.  We are trying to remain in the free tier with the project.

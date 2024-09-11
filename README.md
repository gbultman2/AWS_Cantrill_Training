# AWS_Cantrill_Training
Project Animals4Life through Adrian Cantrill's course.

# Setting Up the Organization
AWS has organizations that can control accounts.  You have a management/master account and have organizational accounts.  It's best to structure the organizational accounts inside of organizational units.  This allows you to apply security control policies to the organizational unit which will then be applied to each account in that group.  

For this project, we set up a general account, a prod account within a prod OU and a dev account within a dev OU.  We didn't use an SCP, but I have one in the diagram just to show where they go and what they apply to.  The SCP can be set up to restrict access to certain products within AWS.  For instance, we could set it up to deny access to S3 and accounts within the OU would not be able to access S3.

With this setup, I can log in to the general account and assume an admin role in either the dev or prod accounts.

![organization](organization.jpg)

## Cloud Trail and Cloud Watch Logs
I enabled a cloud trail across all of the accounts in the organization.  It will record API events for management actions on the account.  This includes things like changing IAM policies, creating EC2 instances, creating S3 buckets etc.  We set it up to monitor events in all AWS regions.  Data events and insights events are disabled as they will likely incur costs.  We are trying to remain in the free tier with the project.


# S3 Multi Region Access Points - Mini Project

I am going to create an S3 multi region access point. Access points let you create a single global s3 endpoint and point it to multiple s3 buckets.  It allows routing to the closest s3 service.

The first thing to do is to set up buckets in two different regions.

```
multi-region-demo-nvirginia
multi-region-demo-sydney-1
```

Next I create an S3 Multi-Region Access Point, `multi-region-access-point`.

Next, we'll enable replication between the two buckets.  We can do that by selecting the multi-region access point and configuring replication.  We'll make it active-active so that both buckets will have the same objects.
![replication](s3replication.png)

Make some files in cloud shell and upload them to the closest s3 bucket using the ARN of the multi-access point.
```
dd if=/dev/urandom of=test1.file bs=1M count=10
aws s3 cp test1.file s3://<s3mulit-access-arn>
```
When running this command in us-east-2 on cloud shell, it will upload to the us-east-1 region bucket then replication will take place.  The access point picks the closest region to upload.

Note that access might not be immediate for users in Sydney when using the multi-region access point since replication takes a little bit.


# Other Tasks Along the Way - Demos
## Static Website - S3

I set up a new bucket, enabled public access on create, uploaded files, and allowed static website hosting. 

When I first try to navigate to the webpage, we get a 403 Forbidden error.  This is because I haven't set up any permissions for anyone to access S3 objects.  I am trying to access as an anonymous user when I open it through a web browser.  The anonymous user is unauthenticated.

To do this, we make a resource policy for the bucket that allows access to **objects in the bucket.**  The "Principal" of "*" gives access to anyone on the web.
```
{
    "Version":"2012-10-17",
    "Statement":[
      {
        "Sid":"PublicRead",
        "Effect":"Allow",
        "Principal": "*",
        "Action":["s3:GetObject"],
        "Resource":["arn:aws:s3:::examplebucket/*"]
      }
    ]
  }
```
Now, we can see the website for the top 10 animals...judged by Merlin the cat.
![judge](merlin_judge.png)

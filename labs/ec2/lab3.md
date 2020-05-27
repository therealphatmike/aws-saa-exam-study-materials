# EC2 Lab 3
## Table of Contents
1. [EC2 and IAM](#ec2andiam)
2. [Instance Metadata](#instancemetadata)
3. [EFS with Multiple EC2 Instances](#efswithmultipleec2instances)
4. [Bootstrap Script](#bootstrapscript)

# EC2 And IAM
In this part of the lab we are going to learn how IAM Roles can help make accessing other AWS services from EC2 instance more secure by allowing us to not have to store our credentials on our EC2 instances.

Steps:

1. Ensure we have at least 1 S3 bucket in our account
2. Create an EC2 instance
3. Use our AWS creds on that EC2 instance to gain access to S3
4. Delete our `.aws/` directory
5. Confirm we no longer have access to S3
6. Attach a Role to the EC2 Instance in the AWS Console
7. Show that the role gives us access to S3 inside our EC2 instance
8. Confirm our EC2 instance doesn't have credentials in `.aws/` showing that it is, indeed, more secure

Now that you have a high level view of our objective, let's dive in.

I am going to start with assuming step 1 is taken care of. We know how to create S3 buckets, so, if you don't have any in your account right now, go add a couple. Now what are are going to do is create a bog-standard t2-micro instance with all the defaults, and we're going to pass in the [bootstrap script](#bootstrapscript) that is at the bottom of this file.

Let's now create an EC2 instance. Just use all the standard options -- Amazon Linux 2, t2-micro...blah blah. Once that is up and running, ssh into it using ec2-user and the pem file you selected at launch.

Once you're in your EC2 instance go ahead and navigate to the root dir. Once you're there, run `aws configure` and enter the access key and secret for your AWS user (I told you to keep those in a safe place). At this point, you should be able to run `aws s3 ls` and see a list of the S3 buckets in your account.

Great, go ahead and delete the `.aws/` dir by running `rm -rf .aws/`. If you rerun `aws s3 ls` again, you should get an authentication error.

Let's head back to the AWS Console, so we can add an IAM Role to our EC2 instance that will allow us to again be able to access S3 from our EC2 instance.

Ok, we're going to head to IAM and create a new role, unless you already have an AdminAccess role. So, once you're in the IAM dashboard, lets click 'Roles' under Access Management on the left hand menu. Then click 'create role'. Select EC2 as the service that will use the role. Click next. Select AdministratorAccess (this should be provided by amazon). Click next. Add any tags you desire, then click next. Name the role 'AdminAccess', and create.

Now we can head over to the EC2 console. Select your instance and click the actions drop down. From there hover over `Instance Settings` and then select Attach/Replace IAM role. This will bring up a menu where you can attach the previously mentioned/created IAM role to this EC2 instance. Once the role is selected, click `Apply`.

Head back over to your terminal, and now, if you type run `aws s3 ls` you should see your list of S3 buckets again! Instead of having to store your access key and secret on your EC2 instance, you can now use roles, which keeps your credentials safe.

I also want to take the time to point out here that if a user were to hack your EC2 instance, they could still do some damage if the role used is admin access, though they won't have access to your client access key and secret. The malicious user could still run up your bill. This is also why it is important to follow least-privilege when creating IAM roles for your user/services to use to access other AWS resources. Remember, least privilege just means the minimal permissions to do the job assigned.

Hopefully this was fun and useful, in the next section, we're going to stay logged in to our ssh session and learn how to gather metadata from the terminal.

# Instance Metadata
In this portion of the lab, we are going to learn how to pull metadata off of our EC2 instances.

You should be logged into your EC2 instance at this point.

AWS provides a special IP address that you can curl different endpoints of to get metadata about your instance from within your instance.

`169.254.169.254`

If we were to run a command such as `curl http://169.254.169.254/some/path` we would get back the data associated with that particular URI. As an example lets run `curl http://169.254.169.254/latest/user-data`. What do you see? That's the bootstrap script we used to bootstrap our EC2 instance launch!

If we swap out `/some/path` for `/latest/meta-data/`, we should see a list of path extensions that we can add to that command. One of these should be `public-ipv4`. So if we run `curl http://169.254.169.254/latest/meta-data/public-ipv4`, we should see the IPv4 address we used to log into this EC2 instance.

# Bootstrap Script
```#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>Hello Cloud Gurus Welcome To My Webpage</h1></html>" > index.html
aws s3 mb s3://YOURBUCKETNAMEHERE
aws s3 cp index.html s3://YOURBUCKETNAMEHERE
```
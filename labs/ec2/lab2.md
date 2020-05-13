# EC2 Lab 2
The goal of this lab is to gain hands on experience with EC2 security groups and EBS concepts. We will do this by creating 2 EC2 instances.

Contents:
- [Security Groups](#security-groups)
- [EBS Volumes](#ebs-volumes)
- [Launch Script Explanation](#launch-script-explanation)

## Security Groups
We're going to log into the AWS console, and navigate to the EC2 Dashboard. Once there, click launch instance. We're going to launch an Amazon Linux 2 instance. 

On the Configure Instance Details go to advanced details and paste the following script into the user details text box

```
#!/bin/bash
yum update -y
yum install httpd -y
cd /var/www/html
echo "<html><body><h1>Now your security group has changed!</h1></body></html>" >> index.html
chkconfig httpd on
service httpd start
```

[Launch Script Explanation](#launch-script-explanation)

When you get to the Security Group page, select create new security group. Give it a name, and make sure it only allows ingress on port 22 (ssh). We are going to launch this instance as is, and go in later and edit the security group so that we can demonstrate that Security Group changes take effect immediately, and that an added ingress rule, will auto-add and egress rule for the same port/traffic type.

Launch your instance.

Once your instance is launched, go to the EC2 console and copy the public IPv4 address. If you open a new tab and try to hit this ip, you will get a gateway timeout. Remeber - we didn't allow inbound http traffic in our security group. Let's go fix that and watch the access immediately be granted.

If you select security groups from the right hand menu on the EC2 dashboard, you should be taken to a list view of all of your security groups. Select the appropriate one (if you don't remember you can look at your EC2 instance details and find the security group name). On the inbound rules tab, select `Edit Inbound Rules`/. Click `Add Rule` and setup a rule to allow inbound http traffic for all IPs. This will be `0.0.0.0/0, ::/0`. The one before the comma is all IPv4 addresses and the one after the comma is all IPv6 addresses. Click `Save Rules`. Open the tab where we tried to access the EC2 instance earlier, and you should now see your HTML.

Do not terminate this EC2 instance as we will be using it in the next portion of the lab to create a custom AMI to launch a new EC2 instance with.

## EBS Volumes
So to learn a little more about how we can take advantage of EBS volumes, we are going to create a new EC2 instance from a custom AMI. There are a few steps to this process, but in the end we should have a cloned instance of the instance we just created for the Security Group portion.

### Creating the snapshot
First thing we need to do is pause our current EC2 instance so we can take a snapshot. Then we're going to navigate to the Volumes dashboard on the right hand menu.

Once we're there, select the corresponding EBS volume for the instance we launched in the previous section. In the `Actions` drop down, select `Create Snapshot`. Give it a description and a `Name` tag, if you wish.

### Creating the AMI
Once we've gotten our snapshot created, it is time to turn it into an AMI. We do this by navigating to the `Snapshots` menu on the left hand EC2 menu bar. Select the Snapshot we just created. Select `Create Image` from the Actions dropdown. Give it a name and description and hit `create`.

### Launch the EC2 Instance
Now that we have our AMI, we can navigate to `AMIs` under the `Images` portion of the EC2 menu on the left hand side. Select your image and click launch. From here, it's business as usual - you can add additional volumes, select a new instance type, etc, but all your EBS volume data from the state you stopped your EC2 instance at will be moved over.

## Launch Script Explanation
`yum update` installs any packages that need updating.

`yum install httpd` installs apache httpd - a simple http server.

The next two commands move us into the root html dir of our http server and place content into an `index.html` file - the file that apache httpd serves on the root path of our site.

`chkconfig httpd on` will tell our system to restart the httpd service if our instance gets rebooted.

`service httpd start` starts the httpd server.
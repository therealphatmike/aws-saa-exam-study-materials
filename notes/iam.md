# IAM

* [IAM FAQs](https://aws.amazon.com/iam/faqs/)

- IAM 101

    ## IAM: Identity Access Management

    What is IAM:

    Allows management of users and their level of access to the AWS Console.

    This is important for the exam, and day to day operations. It comes up A LOT

    Allows us to set up:

    Users: end users such as people, employees of an org, etc

    Groups: A collection of users. Each user in the group will inherit the permissions of the group

    Roles: Allow AWS resources to take certain actions on other AWS resources

    Policies: Defined by policy documents. These are written in JSON and give permission as to what a user/group/role is able to do

    Gives you

    Centralized control

    Shared Access to AWS account

    Granular permissions

    Identity Federation (AD, Facebook, LinkedIn, etc)

    MFA

    Provides temp access for users/devices and services where necessary

    Allows you to set up your own password rotation policy

    Integrates with many AWS services

    Supports PCI DSS Compliance (compliance necessary for taking credit card information)

    IAM is universal. It does not apply to regions at this time.

    The "root" account is simply the account created when you first setup your AWS account. It has complete admin access.

    New users have NO permissions when first created.

    New users are assigned a Access Key ID & Secret Access key when first created.

    These are not the same as a password. You cannot use the Access Key ID and the Secret Access key to log in to the management console. You can use them for API and CLI access, however.

    You only get to view these once. If you lose them, you have to regenerate them. So, save the CSV is a safe spot.

    Always setup MFA on your root account

    You can create and customize own password rotation policies

    ### Exercise: Create a Billing Alarm

    Login to console and make sure region is set to N. Virginia

    Launch Cloudwatch console

    Click on Billing

    Click create alarm at the bottom of the page (the one on the side creates a generic alarm)

    Set Up Alarm

    Create new SNS topic and assign it to your email

    Click link in email to confirm subscription to the alarm

    Add name and description for alarm

    Click create alarm

    NOTE :: *this is a potential exam question — how do you set up an alarm to notify you if a bill goes over a certain point?*
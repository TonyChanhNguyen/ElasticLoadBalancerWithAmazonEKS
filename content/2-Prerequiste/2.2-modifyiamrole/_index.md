---
title : "Modify IAM role"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---
In this step, we will create a IAM Role and assign it to workspace instance.

### Create IAM role
1. Click [IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1) to navigate to IAM service.
2. Click on **Role**.
3. Click on **Create role**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.1.iamrole.png?pc=90pt)
4. At **Trusted entity type** field, select **AWS service**.
5. At **Service or use case** field, select **EC2**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.2.iamrole.png?pc=90pt)
6. Then, click on **Next**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.3.iamrole.png?pc=90pt)

7. At **Permissions policies** field, select policy name **AdministratorAccess**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.4.iamrole.png?pc=90pt)
8. Then, click on **Next**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.5.iamrole.png?pc=90pt)

9. At **Name, review, and create** page, input ```eksworkspace-administrator``` at **Role name** field.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.6.iamrole.png?pc=90pt)

10. Then, scroll down to the end of page and click on **Create role**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.7.iamrole.png?pc=90pt)

### Assign role to workspace instance
1. At **AWS Cloud9** interface, click on **Manage EC2 instance**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.8.iamrole.png?pc=90pt)
2. You will see the created workspace instance. Then, click to select it.
3. Click on **Action**.
4. Click on **Security**.
5. Click on **Modify IAM role**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.9.iamrole.png?pc=90pt)

6. Select the role name **eksworkspace-administrator** which was created at above steps.
7. Then, click on **Update IAM role**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.10.iamrole.png?pc=90pt)

8. New IAM role was updated successfully.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.11.iamrole.png?pc=90pt)

### Update Cloud9 configuration

{{% notice info %}}
Cloud9 will manage IAM credentials automatically. This default configuration is currently not compatible with EKS authentication via IAM, we will need to disable this feature and use the IAM Role.
{{% /notice %}}

1. At **AWS Cloud9** interface, click on **AWS Cloud9**.
2. Select **Preferences**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.12.iamrole.png?pc=90pt)

3. At **AWS Settings**, disable **AWS managed temporary credentials**.
![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.13.iamrole.png?pc=90pt)

4. To ensure that temporary credentials are not saved in **Cloud9**, we will delete all existing credentials with the command below.
```
rm -vf ${HOME}/.aws/credentials
```

![Create IAM role](../../images/2.prerequisites/2.2.iamrole/2.2.14.iamrole.png?pc=90pt)


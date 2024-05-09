---
title : "Amazon EKS Persistent Storage with Amazon EFS"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

### Overview
[Amazon Elastic File System](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html) is a simple, serverless, set-and-forget elastic file system for use with AWS Cloud services and on-premises resources. It's built to scale on demand to petabytes without disrupting applications, growing and shrinking automatically as you add and remove files, eliminating the need to provision and manage capacity to accommodate growth.
![Amazon EKS Persistent Storage with Amazon EFS](../images/4.eksstoragewithefs/eksefs.png?pc=60pt)

### Content
+ [4.1 Install EFS CSI Driver](../4-eksstoragewithefs/4.1-installecsidriver/)
+ [4.2 Create EFS File System](../4-eksstoragewithefs/4.2-createefs/)
+ [4.3 Static Provisioning](../4-eksstoragewithefs/4.3-staticprovision/)
+ [4.4 Dynamic Provisioning](../4-eksstoragewithefs/4.4-dynamicprovision/)
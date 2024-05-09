---
title : "Clean up resources"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

### Delete EKS Cluster
1. Execute the below command to delete EKS cluster.
```
eksctl delete cluster --name fcj-storage-cluster --region ap-southeast-1
```
![Cleanup resources](../images/5.cleanup/5.1.cleanup.png?pc=60pt)

{{% notice info %}}
It will take you about 20 minutes to delete.
{{% /notice %}}

![Cleanup resources](../images/5.cleanup/5.2.cleanup.png?pc=60pt)

### Delete Cloud9 Workspace
1. Go to [Cloud9](https://ap-southeast-1.console.aws.amazon.com/cloud9control/home?region=ap-southeast-1#/).
2. Select **FCJ-Workspace**.
3. Click on **Delete**.


![Clean up resources](../images/5.cleanup/5.3.cleanup.png?pc=90pt)

4. Input ```Delete``` to confirm.
5. Click on **Delete**.
![Clean up resources](../images/5.cleanup/5.4.cleanup.png?pc=90pt)

### Delete IAM Role
1. Delete IAM Role Name **AmazonEKS_EFS_CSI_DriverRole**.
![Clean up resources](../images/5.cleanup/5.5.cleanup.png?pc=90pt)




---
title : "Cleanup resources"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

### Delete Amazon EKS Cluster.
1. At Cloud9 terminal, execute the below command to delete your EKS Cluster.
```
eksctl delete cluster --name fcj-elb-cluster --region ap-southeast-1
```

![Cleanup resources](../../images/7.cleanup/7.1.cleanup.png?pc=90pt)

2. It will take you about 15 minutes to delete successfully.

![Cleanup resources](../../images/7.cleanup/7.2.cleanup.png?pc=90pt)

### Delete AWS Certificate Manager certificate.
1. Go to [AWS Certificate Manager certificate.](https://ap-southeast-1.console.aws.amazon.com/acm/home?region=ap-southeast-1#/certificates/list), select your created certificate.
2. Click on **Delete**.
![Cleanup resources](../../images/7.cleanup/7.3.cleanup.png?pc=90pt)

3. Input ```delete``` to confirm.
4. Then, click on **Delete** to delete.
![Cleanup resources](../../images/7.cleanup/7.4.cleanup.png?pc=90pt)

### Delete created Route 53 Record.
1. Go to your **Host Zone** on [Route 53](https://us-east-1.console.aws.amazon.com/route53/v2/home?region=ap-southeast-1).
2. Select the created Record by AWS Certificate Manager.
3. Click on **Delete record**.
![Cleanup resources](../../images/7.cleanup/7.5.cleanup.png?pc=90pt)

4. Then, click on **Delete** to delete.

### Delete Cloud9 Workspace.
1. Go to [Cloud9](https://ap-southeast-1.console.aws.amazon.com/cloud9control/home?region=ap-southeast-1#/).
2. Select **FCJ-Workspace**.
3. Click on **Delete**.

![Cleanup resources](../../images/7.cleanup/7.6.cleanup.png?pc=90pt)

4. Input ```Delete``` to confirm.
5. Click on **Delete**.
![Cleanup resources](../../images/7.cleanup/7.7.cleanup.png?pc=90pt)
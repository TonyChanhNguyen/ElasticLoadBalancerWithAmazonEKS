---
title : "Install Amazon EBS CSI Driver"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---


The Amazon Elastic Block Store (Amazon EBS) Container Storage Interface (CSI) driver manages the lifecycle of Amazon EBS volumes as storage for the Kubernetes Volumes that you create. The Amazon EBS CSI driver makes Amazon EBS volumes for these types of Kubernetes volumes: generic ephemeral volumes and persistent volumes.

### Create IAM Policy
The Amazon EBS CSI plugin requires IAM permissions to make calls to AWS APIs on your behalf.
1. Go to [IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1).
2. Select **Policies** section.
3. Click on **Create policy**.
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.1.installcsidriver.png?pc=60pt)

4. At **Policy editor** field, select **JSON** tab.
5. Then, paste the below JSON.
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"ec2:AttachVolume",
				"ec2:CreateSnapshot",
				"ec2:CreateTags",
				"ec2:CreateVolume",
				"ec2:DeleteSnapshot",
				"ec2:DeleteTags",
				"ec2:DeleteVolume",
				"ec2:DescribeInstances",
				"ec2:DescribeSnapshots",
				"ec2:DescribeTags",
				"ec2:DescribeVolumes",
				"ec2:DetachVolume"
			],
			"Resource": "*"
		}
	]
}
```
6. Then, click on **Next**.
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.2.installcsidriver.png?pc=60pt)

7. Input ```Amazon_EBS_CSI_Driver``` as **Policy name**.
8. Input ```Policy for EC2 Instances to access Elastic Block Store``` as **Description - optional**.
9. Click on **Create policy**.
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.3.installcsidriver.png?pc=60pt)

### Get the IAM role Worker Nodes using
1. At Cloud9 Terminal, using below command to find out IAM role name that Worker Nodes using.
```
kubectl -n kube-system describe configmap aws-auth
```
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.4.installcsidriver.png?pc=60pt)

Verify the value of **rolearn** to find out the IAM Role name.
*Example*: In this case, the IAM Role name is **eksctl-fcj-storage-cluster-nodegro-NodeInstanceRole-dN18fcwv9euu**.

### Associate policy to IAM Role
1. Go to [IAM Role](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/roles). 
2. At **Search** feature, input the IAM Role name you had found out.
3. Click on it.
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.5.installcsidriver.png?pc=60pt)

4. Click on **Attach policies** of **Add permissions** feature.
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.6.installcsidriver.png?pc=60pt)

5. Search the policy name **Amazon_EBS_CSI_Driver**.
6. Select the result.
7. Click on **Add permissions**.
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.7.installcsidriver.png?pc=60pt)


### Deploy Amazon EBS CSI Driver
1. At Cloud9 Terminal, execute below command.
```
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"
```
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.8.installcsidriver.png?pc=60pt)

2. Verify the result. Make sure that there are **ebs-csi** pods are running at namespace **kube-system**.
```
kubectl get pods -n kube-system
```
![Install CSI Driver](../../images/3.eksstoragewithebs/3.1.installcsidriver/3.1.9.installcsidriver.png?pc=60pt)

#### You had installed Amazon EBS CSI Driver successfully, move to next step to demonstrate how to use it to deploy EBS Storage for Amazon EKS Cluster.
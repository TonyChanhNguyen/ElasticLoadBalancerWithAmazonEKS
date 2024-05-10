---
title : "Create Amazon EKS Cluster"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---

In previous step, we installed necessary tools: awscli, kubectl and eksctl. Now, we will process to create an Amazon EKS Cluster with managed Node Group as EC2 Instance.

### Create Amazon EKS Cluster.
1. At Cloud9 terminal, execute the command the below to create an Amazon EKS Cluster.
```
eksctl create cluster --name=fcj-db-cluster --region=ap-southeast-1 --zones=ap-southeast-1a,ap-southeast-1b --without-nodegroup
```

![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.1.createekscluster.png?pc=60pt)

2. Then, verify the Cluster by command.
```
eksctl get cluster --region=ap-southeast-1
```

![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.2.createekscluster.png?pc=60pt)

3. Enable **kubectl** to communicate with your cluster by adding a new context to the **kubectl config** file.
```
aws eks update-kubeconfig --region=ap-southeast-1 --name=fcj-db-cluster
```
![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.3.createekscluster.png?pc=60pt)

4. Then, confirm communication with your cluster by running the following command.
```
kubectl get svc
```

*Note*: The expected output is the appearance of ClusterIP service. 

![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.4.createekscluster.png?pc=60pt)


### Create and associate IAM OIDC Provider for EKS Cluster.
IAM OpenID Connect (OIDC) Provider help to use some Amazon EKS add-ons, or to enable individual Kubernetes workloads to have specific AWS Identity and Access Management (IAM) permissions.
1. At Cloud9 terminal, execute the command the below to create and associate an OIDC Provider to your Amazon EKS Cluster.
```
eksctl utils associate-iam-oidc-provider --cluster=fcj-db-cluster --region=ap-southeast-1 --approve
```
![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.5.createekscluster.png?pc=60pt)

2. To confirm created OIDC Provider, go to [IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1).
3. Navigate to **Identity providers** section. 
4. You will see there is a new Provider had been created.

![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.6.createekscluster.png?pc=60pt)

### Create Amazon EKS managed Node Group.
1. At Cloud9 terminal, execute the command the below to create managed Node Group and associate it to EKS Cluster.
```
eksctl create nodegroup --name=fcj-db-nodegroup --cluster=fcj-db-cluster --region=ap-southeast-1 --node-type=t3.medium --nodes=1
```

![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.7.createekscluster.png?pc=60pt)

2. It will take you about 15 minutes to finish this process.
![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.8.createekscluster.png?pc=60pt)

3. List existing nodes in cluster.
```
kubectl get nodes
```
![Create EKS Cluster](../../images/2.prerequisites/2.4.createekscluster/2.4.9.createekscluster.png?pc=60pt)


---
title : "Install Amazon EFS CSI Driver"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

### Create IAM Role
The Amazon EFS CSI driver requires IAM permissions to interact with your file system. Create an IAM role and attach the required AWS managed policy to it.
1. At Cloud9 Terminal, run the following commands to create the IAM role.
```
export cluster_name=fcj-storage-cluster
export role_name=AmazonEKS_EFS_CSI_DriverRole
eksctl create iamserviceaccount \
    --name efs-csi-controller-sa \
    --namespace kube-system \
    --cluster $cluster_name \
    --region ap-southeast-1 \
    --role-name $role_name \
    --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEFSCSIDriverPolicy \
    --override-existing-serviceaccounts \
    --approve
TRUST_POLICY=$(aws iam get-role --role-name $role_name --query 'Role.AssumeRolePolicyDocument' | \
    sed -e 's/efs-csi-controller-sa/efs-csi-*/' -e 's/StringEquals/StringLike/')
aws iam update-assume-role-policy --role-name $role_name --policy-document "$TRUST_POLICY"
```
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.1.installecsidriver.png?pc=60pt)

Verify created Service Account.

2. Execute below command to verify the Service Account name ```efs-csi-controller-sa``` in ```kube-system``` namespace.
```
kubectl get sa efs-csi-controller-sa -n kube-system
```
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.2.installecsidriver.png?pc=60pt)
Verify the created IAM Role.

3. Go to [IAM Role](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/roles).
4. Input the IAM Role Name ```AmazonEKS_EFS_CSI_DriverRole``` at **Search** feature.
5. Click on IAM Role **AmazonEKS_EFS_CSI_DriverRole**.
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.3.installecsidriver.png?pc=60pt)

6. There is a policy named **AmazonEFSCSIDriverPolicy** attached to IAM Role.
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.4.installecsidriver.png?pc=60pt)

5. Navigate to **Trust replationships** tab.
6. Verify that there is a condition with value is **system:serviceaccount:kube-system:efs-csi-**.
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.5.installecsidriver.png?pc=60pt)

### Install the Amazon EFS CSI driver
1. Get the version of cluster.
```
kubectl version
```

![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.6.installecsidriver.png?pc=60pt)
The version of cluster is **Server Version** (1.29 not 1.29.3).
2. View the names of add-ons available for a cluster version.
```
eksctl utils describe-addon-versions --region=ap-southeast-1 --kubernetes-version 1.29 | grep AddonName
```
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.7.installecsidriver.png?pc=60pt)


3. Determine the current add-ons and add-on versions installed on your cluster
```
eksctl get addon --cluster fcj-storage-cluster --region ap-southeast-1
```
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.10.installecsidriver.png?pc=60pt)


4. Get **Service Account Role ARN** of **efs-csi-controller-sa** service account.
```
kubectl describe sa efs-csi-controller-sa -n kube-system 
```
5. Save the value of **eks.amazonaws.com/role-arn** at **Annotations** field.

![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.8.installecsidriver.png?pc=60pt)

6. Create an Amazon EKS add-on. Replace with specific value on command **eksctl create addon --cluster <REPLACE_CLUSTER_NAME> --name <REPLACE_ADDON_NAME> --region <REPLACE_REGION_ID> --version latest --service-account-role-arn <REPLACE_IAM_ROLE_ARN> --force**. Then execute the command.
```
eksctl create addon --cluster fcj-storage-cluster --name aws-efs-csi-driver --region ap-southeast-1 --version latest \
--service-account-role-arn arn:aws:iam::170074558790:role/AmazonEKS_EFS_CSI_DriverRole --force
```
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.9.installecsidriver.png?pc=60pt)

7. Determine the current add-ons and add-on versions installed on your cluster again.
```
eksctl get addon --cluster fcj-storage-cluster --region ap-southeast-1
```
![Install CSI Driver](../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.11.installecsidriver.png?pc=60pt)
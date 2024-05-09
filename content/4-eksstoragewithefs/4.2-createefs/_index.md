---
title : "Create EFS File System"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

Amazon EFS CSI driver supports dynamic provisioning and static provisioning. Currently, Dynamic Provisioning creates an access point for each PV. This mean an Amazon EFS file system has to be created manually on AWS first and should be provided as an input to the storage class parameter. For static provisioning, the Amazon EFS file system needs to be created manually on AWS first. After that, it can be mounted inside a container as a volume using the driver.

### Create Security Group
1. Retrieve the VPC ID that your cluster is in and store it in a variable for use in a later step
```
vpc_id=$(aws eks describe-cluster \
    --name fcj-storage-cluster \
    --query "cluster.resourcesVpcConfig.vpcId" \
    --output text)
echo $vpc_id
```

![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.1.createefs.png?pc=60pt)

2. Retrieve the CIDR range for your cluster's VPC and store it in a variable for use in a later step
```
cidr_range=$(aws ec2 describe-vpcs \
    --vpc-ids $vpc_id \
    --query "Vpcs[].CidrBlock" \
    --output text \
    --region ap-southeast-1)
echo $cidr_range
```

![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.2.createefs.png?pc=60pt)

Now we will create a security group with an inbound rule that allows inbound NFS traffic for your Amazon EFS mount points.

3. Create a security group
```
security_group_id=$(aws ec2 create-security-group \
    --group-name MyEfsSecurityGroup \
    --description "My EFS security group" \
    --vpc-id $vpc_id \
    --output text)
```
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.3.createefs.png?pc=60pt)

4. Create an inbound rule that allows inbound NFS traffic from the CIDR for your cluster's VPC.
```
aws ec2 authorize-security-group-ingress \
    --group-id $security_group_id \
    --protocol tcp \
    --port 2049 \
    --cidr $cidr_range
```
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.4.createefs.png?pc=60pt)

### Create EFS File System
1. Create a file system.
```
file_system_id=$(aws efs create-file-system \
    --region ap-southeast-1 \
    --performance-mode generalPurpose \
    --query 'FileSystemId' \
    --output text)
echo $file_system_id
```
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.5.createefs.png?pc=60pt)

2. Go to [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems) to verify.

There is a created file system with **File system ID** match with value of **file_system_id**.

![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.6.createefs.png?pc=60pt)

3. Click on the EFS file system.
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.7.createefs.png?pc=60pt)

Now we will create a mount target
4. Navigate to **Network** tab.
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.8.createefs.png?pc=60pt)
There is no **Mount Target** created.

5. Determine the IP address of your cluster nodes.
```
kubectl get nodes
```
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.9.createefs.png?pc=60pt)

In my case, the private IP address of cluster node is 192.168.18.22.

6. Determine the IDs of the subnets in your VPC and which Availability Zone the subnet is in.
```
aws ec2 describe-subnets \
    --region ap-southeast-1 \
    --filters "Name=vpc-id,Values=$vpc_id" \
    --query 'Subnets[*].{SubnetId: SubnetId,AvailabilityZone: AvailabilityZone,CidrBlock: CidrBlock}' \
    --output table
```
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.10.createefs.png?pc=60pt)

Cluster node with IP is 192.168.18.22 belongs to CIDR Block **192.168.0.0/19** and SubnetId **subnet-0a75ff93096b88a38**.

7. Add mount targets for the subnets that your nodes are in. Replace with your ```subnet-id```.
```
aws efs create-mount-target \
    --file-system-id $file_system_id \
    --subnet-id subnet-0a75ff93096b88a38 \
    --security-groups $security_group_id
```
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.11.createefs.png?pc=60pt)

8. Go to **Network** tab of [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems) to verify the mount target again.
![Create EFS File System](../../images/4.eksstoragewithefs/4.2.createefs/4.2.12.createefs.png?pc=60pt)

There is a new created target mount appeared.

#### You had create a EFS File System and its Mount Target successfully.
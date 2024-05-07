---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

Before we dive into the implementation, below is a summary of the two AWS storage services we'll utilize and integrate with EKS:

+ [Amazon Elastic Block Store](https://aws.amazon.com/ebs/) (supports EC2 only): a block storage service that provides direct access from EC2 instances and containers to a dedicated storage volume designed for both throughput and transaction-intensive workloads at any scale.
+ [Amazon Elastic File System](https://aws.amazon.com/efs/) (supports Fargate and EC2): a fully managed, scalable, and elastic file system well suited for big data analytics, web serving and content management, application development and testing, media and entertainment workflows, database backups, and container storage. EFS stores your data redundantly across multiple Availability Zones (AZ) and offers low latency access from Kubernetes pods irrespective of the AZ in which they are running.
+ [Amazon FSx for NetApp ONTAP](https://aws.amazon.com/fsx/netapp-ontap/) (supports EC2 only): Fully managed shared storage built on NetApp’s popular ONTAP file system. FSx for NetApp ONTAP stores your data redundantly across multiple Availability Zones (AZ) and offers low latency access from Kubernetes pods irrespective of the AZ in which they are running.
+ [FSx for Lustre](https://aws.amazon.com/fsx/lustre/) (supports EC2 only): a fully managed, high-performance file system optimized for workloads such as machine learning, high-performance computing, video processing, financial modeling, electronic design automation, and analytics. With FSx for Lustre, you can quickly create a high-performance file system linked to your S3 data repository and transparently access S3 objects as files.
+ [Amazon Simple Storage Service](https://aws.amazon.com/s3) (supports EC2 only): is an object storage service offering industry-leading scalability, data availability, security, and performance. Customers of all sizes and industries can store and protect any amount of data for virtually any use case, such as data lakes, cloud-native applications, and mobile apps. With cost-effective storage classes and easy-to-use management features, you can optimize costs, organize data, and configure fine-tuned access controls to meet specific business, organizational, and compliance requirements.

It's also very important to be familiar with some concepts about [Kubernetes Storage](https://kubernetes.io/docs/concepts/storage/):

+ [Volumes](): On-disk files in a container are ephemeral, which presents some problems for non-trivial applications when running in containers. One problem is the loss of files when a container crashes. The kubelet restarts the container but with a clean state. A second problem occurs when sharing files between containers running together in a Pod. The Kubernetes volume abstraction solves both of these problems. Familiarity with Pods is suggested.
+ [Ephemeral Volumes]() are designed for these use cases. Because volumes follow the Pod's lifetime and get created and deleted along with the Pod, Pods can be stopped and restarted without being limited to where some persistent volume is available.
+ [Persistent Volumes (PV)]() is a piece of storage in a cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. It's a resource in the cluster just like a node is a cluster resource. PVs are volume plugins like Volumes, but have a lifecycle independent of any individual Pod that uses the PV. This API object captures the details of the implementation of the storage, be that NFS, iSCSI, or a cloud-provider-specific storage system.
+ [Persistent Volume Claim (PVC)]() is a request for storage by a user. It's similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany or ReadWriteMany, see AccessModes
+ [Storage Classes]() provides a way for administrators to describe the "classes" of storage they offer. Different classes might map to quality-of-service levels, or to backup policies, or to arbitrary policies determined by cluster administrators. Kubernetes itself is unopinionated about what classes represent. This concept is sometimes called "profiles" in other storage systems.
+ [Dynamic Volume]() Provisioning allows storage volumes to be created on-demand. Without dynamic provisioning, cluster administrators have to manually make calls to their cloud or storage provider to create new storage volumes, and then create PersistentVolume objects to represent them in Kubernetes. The dynamic provisioning feature eliminates the need for cluster administrators to pre-provision storage. Instead, it automatically provisions storage when it is requested by users.



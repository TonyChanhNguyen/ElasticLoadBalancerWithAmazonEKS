---
title : "Cài đặt Amazon EFS CSI Driver"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

### Tạo IAM Role
Amazon EFS CSI driver yêu cầu quyền IAM để tương tác với hệ thống tệp của bạn. Tạo vai trò IAM và đính kèm chính sách được quản lý AWS cần thiết vào vai trò đó.
1. Tại cửa sổ lệnh Cloud9, thực thi câu lệnh dưới đây để tạo IAM role.
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
![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.1.installecsidriver.png?pc=60pt)

Xác minh Service Account đã tạo.

2. Thực thi câu lệnh dưới để kiểm tra Service Account tên ```efs-csi-controller-sa``` trong namespace ```kube-system```.
```
kubectl get sa efs-csi-controller-sa -n kube-system
```
![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.2.installecsidriver.png?pc=60pt)
Xác minh IAM Role đã tạo.

3. Đi đến [IAM Role](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/roles).
4. Nhập tên IAM Role ```AmazonEKS_EFS_CSI_DriverRole``` tại tính năng **Search**.
5. Nhấn IAM Role **AmazonEKS_EFS_CSI_DriverRole**.
![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.3.installecsidriver.png?pc=60pt)

6. Có một Policy tên **AmazonEFSCSIDriverPolicy** được gắn vào IAM Role.
![Install CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.4.installecsidriver.png?pc=60pt)

5. Chuyển đến thanh điều hướng **Trust replationships**.
6. Xác minh rằng có một điều kiện với giá trị **system:serviceaccount:kube-system:efs-csi-**.
![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.5.installecsidriver.png?pc=60pt)

### Cài đặt Amazon EFS CSI driver
1. Kiểm tra phiên bản của Cluster.
```
kubectl version
```

![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.6.installecsidriver.png?pc=60pt)
Phiên bản của Cluster là **Server Version** (1.29 không phải 1.29.3).
2. Kiểm tra các tên của tiện ích bổ sung khả dụng cho phiên bản Cluster.
```
eksctl utils describe-addon-versions --region=ap-southeast-1 --kubernetes-version 1.29 | grep AddonName
```
![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.7.installecsidriver.png?pc=60pt)


3. Xác định các tiện ích bổ sung hiện tại và phiên bản tiện ích bổ sung được cài đặt trên cụm của bạn.
```
eksctl get addon --cluster fcj-storage-cluster --region ap-southeast-1
```
![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.10.installecsidriver.png?pc=60pt)


4. Lấy **Service Account Role ARN** của **efs-csi-controller-sa** service account.
```
kubectl describe sa efs-csi-controller-sa -n kube-system 
```
5. Lưu giá trị của **eks.amazonaws.com/role-arn** ở mục **Annotations**.

![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.8.installecsidriver.png?pc=60pt)

6. Tạo một tiện ích bổ sung Amazon EKS. Thay thế các giá trị cụ thể trong câu lệnh **eksctl create addon --cluster <REPLACE_CLUSTER_NAME> --name <REPLACE_ADDON_NAME> --region <REPLACE_REGION_ID> --version latest --service-account-role-arn <REPLACE_IAM_ROLE_ARN> --force**. Sau đó tiến hành thực thi.
```
eksctl create addon --cluster fcj-storage-cluster --name aws-efs-csi-driver --region ap-southeast-1 --version latest \
--service-account-role-arn arn:aws:iam::170074558790:role/AmazonEKS_EFS_CSI_DriverRole --force
```
![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.9.installecsidriver.png?pc=60pt)

7. Xác định lại các tiện ích bổ sung hiện tại và phiên bản của tiện ích bổ sung đã được cài đặt bên trong Cluster.
```
eksctl get addon --cluster fcj-storage-cluster --region ap-southeast-1
```
![Cài đặt CSI Driver](../../../images/4.eksstoragewithefs/4.1.installecsidriver/4.1.11.installecsidriver.png?pc=60pt)
---
title : "Cài đặt Amazon EBS CSI Driver"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---


Amazon Elastic Block Store (Amazon EBS) Container Storage Interface (CSI) driver quản lý vòng đời của các ổ đĩa Amazon EBS dưới dạng bộ lưu trữ cho các ổ đĩa Kubernetes mà bạn tạo. Amazon EBS CSI driver tạo các ổ đĩa Amazon EBS cho các loại ổ đĩa Kubernetes sau: ổ đĩa tạm thời và ổ đĩa cố định.

### Tạo IAM Policy
Amazon EBS CSI plugin yêu cầu quyền IAM để gọi đến AWS APIs.
1. Đi đến [IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1).
2. Chọn mục **Policies**.
3. Chọn **Create policy**.
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.1.installcsidriver.png?pc=60pt)

4. Tại mục **Policy editor**, chọn **JSON**.
5. Sau đó, dán đoạn JSON dưới đây.
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
6. Sau đó, nhấn **Next**.
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.2.installcsidriver.png?pc=60pt)

7. Nhập ```Amazon_EBS_CSI_Driver``` là **Policy name**.
8. Nhập ```Policy for EC2 Instances to access Elastic Block Store``` là **Description - optional**.
9. Nhấn **Create policy**.
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.3.installcsidriver.png?pc=60pt)

### Liệt kê IAM role Worker Nodes đang sử dụng
1. Tại cửa sổ lệnh Cloud9, sử dụng câu lệnh bên dưới để tìm tên của IAM role mà Worker Nodes đang sử dụng.
```
kubectl -n kube-system describe configmap aws-auth
```
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.4.installcsidriver.png?pc=60pt)

Kiếm tra giá trị của **rolearn** để tìm kiếm tên của IAM Role.
*Ví dụ*: Trong trường hợp này, tên IAM Role là **eksctl-fcj-storage-cluster-nodegro-NodeInstanceRole-dN18fcwv9euu**.

### Liên kết policy vào IAM Role
1. Đi đến [IAM Role](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/roles). 
2. Tại tính năng **Search**, nhập tên IAM Role vừa tìm thấy.
3. Nhấn vào nó.
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.5.installcsidriver.png?pc=60pt)

4. Nhấn **Attach policies** của tính năng **Add permissions**.
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.6.installcsidriver.png?pc=60pt)

5. Tìm kiếm policy tên **Amazon_EBS_CSI_Driver**.
6. Chọn kết quả tìm được.
7. Nhấn **Add permissions**.
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.7.installcsidriver.png?pc=60pt)


### Triển khai Amazon EBS CSI Driver
1. Tại cửa sổ lệnh Cloud9, thực thi câu lệnh bên dưới.
```
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"
```
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.8.installcsidriver.png?pc=60pt)

2. Kiểm tra kết quả. Đảm bảo có pod tên **ebs-csi** đang chạy ở namespace **kube-system**.
```
kubectl get pods -n kube-system
```
![Cài đặt CSI Driver](../../../images/3.eksdbwithebs/3.1.installcsidriver/3.1.9.installcsidriver.png?pc=60pt)

#### Bạn đã cài đặt Amazon EBS CSI Driver thành công, chuyển đến bước tiếp theo để tìm hiểu làm thế nào để sử dụng Amazon EBS CSI Driver để triển khai bộ lưu trữ EBS cho Amazon EKS Cluster.
---
title : "Dọn dẹp tài nguyên"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

### Xóa EKS Cluster
1. Thực hiện câu lệnh dưới để xóa EKS cluster.
```
eksctl delete cluster --name fcj-storage-cluster --region ap-southeast-1
```
![Dọn dẹp tài nguyên](../../images/5.cleanup/5.1.cleanup.png?pc=60pt)

{{% notice info %}}
Sẽ mất khoảng 20 phút để hoàn thành việc xóa
{{% /notice %}}

![Dọn dẹp tài nguyên](../../images/5.cleanup/5.2.cleanup.png?pc=60pt)

### Xóa Cloud9 Workspace
1. Đi đến [Cloud9](https://ap-southeast-1.console.aws.amazon.com/cloud9control/home?region=ap-southeast-1#/).
2. Chọn **FCJ-Workspace**.
3. Nhấn **Delete**.


![Dọn dẹp tài nguyên](../../images/5.cleanup/5.3.cleanup.png?pc=90pt)

4. Nhập ```Delete``` để xác nhận.
5. Nhấn **Delete**.
![Dọn dẹp tài nguyên](../../images/5.cleanup/5.4.cleanup.png?pc=90pt)

### Xóa IAM Role
1. Xóa IAM Role tên **AmazonEKS_EFS_CSI_DriverRole**.
![Dọn dẹp tài nguyên](../../images/5.cleanup/5.5.cleanup.png?pc=90pt)




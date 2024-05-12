---
title : "Dọn dẹp tài nguyên"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

### Xóa EKS Cluster
1. Thực thi câu lệnh bên dưới để xóa EKS Cluster.
```
eksctl delete cluster --name fcj-db-cluster --region ap-southeast-1
```
![Dọn dẹp tài nguyên](../../../images/5.cleanup/5.1.cleanup.png?pc=60pt)

{{% notice info %}}
Sẽ mất khoảng 20 phút để xóa.
{{% /notice %}}

![Dọn dẹp tài nguyên](../../../images/5.cleanup/5.2.cleanup.png?pc=60pt)

### Xóa Cloud9 Workspace
1. Đi đến [Cloud9](https://ap-southeast-1.console.aws.amazon.com/cloud9control/home?region=ap-southeast-1#/).
2. Chọn **FCJ-Workspace**.
3. Nhấn **Delete**.

![Dọn dẹp tài nguyên](../../../images/5.cleanup/5.3.cleanup.png?pc=60pt)

4. Nhập ```Delete``` để xác nhận.
5. Nhấn **Delete**.
![Clean up resources](../../../images/5.cleanup/5.4.cleanup.png?pc=90pt)


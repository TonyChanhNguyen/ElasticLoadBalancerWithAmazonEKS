---
title : "Dọn dẹp tài nguyên"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

### Xóa Amazon EKS Cluster.
1. Tại cửa sổ lệnh Cloud9, thực thi câu lệnh bên dưới để xóa EKS Cluster.
```
eksctl delete cluster --name fcj-elb-cluster --region ap-southeast-1
```

![Cleanup resources](../../images/7.cleanup/7.1.cleanup.png?pc=90pt)

2. Sẽ mất khoảng 15 phút để hoàn tất.

![Cleanup resources](../../images/7.cleanup/7.2.cleanup.png?pc=90pt)

### Xóa chứng chỉ AWS Certificate Manager.
1. Đi đến [AWS Certificate Manager certificate.](https://ap-southeast-1.console.aws.amazon.com/acm/home?region=ap-southeast-1#/certificates/list), chọn chứng chỉ mà bạn đã tạo.
2. Nhấn **Delete**.
![Cleanup resources](../../images/7.cleanup/7.3.cleanup.png?pc=90pt)

3. Nhập ```delete``` để xác nhận.
4. Sau đó, nhấn **Delete** để xóa.
![Cleanup resources](../../images/7.cleanup/7.4.cleanup.png?pc=90pt)

### Xóa Route 53 Record đã tạo.
1. Đi đến **Host Zone** của bạn trên [Route 53](https://us-east-1.console.aws.amazon.com/route53/v2/home?region=ap-southeast-1).
2. Chọn bản ghi được tạo bởi AWS Certificate Manager.
3. Nhấn **Delete record**.
![Cleanup resources](../../images/7.cleanup/7.5.cleanup.png?pc=90pt)

4. Sau đó, nhấn **Delete** để xóa.

### Xóa Cloud9 Workspace.
1. Đi đến [Cloud9](https://ap-southeast-1.console.aws.amazon.com/cloud9control/home?region=ap-southeast-1#/).
2. Chọn **FCJ-Workspace**.
3. Nhấn **Delete**.

![Cleanup resources](../../images/7.cleanup/7.6.cleanup.png?pc=90pt)

4. Nhập ```Delete``` để xác nhận.
5. Nhấn **Delete**.
![Cleanup resources](../../images/7.cleanup/7.7.cleanup.png?pc=90pt)
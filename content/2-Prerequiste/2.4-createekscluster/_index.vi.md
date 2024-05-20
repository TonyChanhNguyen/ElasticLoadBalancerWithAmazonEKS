---
title : "Tạo Amazon EKS Cluster"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---

Ở bước trước đó, chúng ta đã cài đặt các công cụ cần thiết: awscli, kubectl và eksctl. Bây giờ chúng ta sẽ tạo một Amazon EKS Cluster với managed Node Group là EC2 Instance.

### Tạo Amazon EKS Cluster.
1. Tại cửa sổ lệnh Cloud9, thực hiện câu lệnh này để tạo một Amazon EKS Cluster.
```
eksctl create cluster --name=fcj-elb-cluster --region=ap-southeast-1 --zones=ap-southeast-1a,ap-southeast-1b --without-nodegroup
```

![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.1.createekscluster.png?pc=60pt)

2. Sau đó, kiểm tra Cluster bằng câu lệnh.
```
eksctl get cluster --region=ap-southeast-1
```

![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.2.createekscluster.png?pc=60pt)

3. Cho phép **kubectl** giao tiếp với cluster của bạn bằng việc thêm chuỗi này vào tệp **kubectl config**.
```
aws eks update-kubeconfig --region=ap-southeast-1 --name=fcj-elb-cluster
```
![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.3.createekscluster.png?pc=60pt)

4. Sau đó, xác nhận giao tiếp với cluster của bạn bằng câu lệnh.
```
kubectl get svc
```

*Chú ý*: Kết quả mong đợi là sự xuất hiện của dịch vụ ClusterIP

![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.4.createekscluster.png?pc=60pt)


### Tạo và liên kết IAM OIDC Provider cho EKS Cluster.
IAM OpenID Connect (OIDC) Provider giúp sử dụng một số tiện ích của Amazon EKS, hoặc cho phép khối lượng công việc riêng lẻ Kubernetes có các quyền AWS Identity and Access Management (IAM) cụ thể.
1. Tại cửa sổ lệnh Cloud9, thực hiện câu lệnh này để tạo và liên kết một OIDC Provider tới Amazon EKS Cluster.
```
eksctl utils associate-iam-oidc-provider --cluster=fcj-elb-cluster --region=ap-southeast-1 --approve
```
![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.5.createekscluster.png?pc=60pt)

2. Để xác nhận OIDC Provider đã tạo, đi đến [IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1).
3. Chuyển đến mục **Identity providers**. 
4. Bạn sẽ thấy có một Provider được tạo

![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.6.createekscluster.png?pc=60pt)

### Tạo Amazon EKS managed Node Group.
1. Tại cửa sổ lệnh Cloud9, thực hiện câu lệnh này để tạo managed Node Group và liên kết nó tới EKS Cluster.
```
eksctl create nodegroup --name=fcj-elb-nodegroup --cluster=fcj-elb-cluster --region=ap-southeast-1 --node-type=t3.medium --nodes=1 --node-private-networking  
```

![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.7.createekscluster.png?pc=60pt)

2. Sẽ mất khoảng 15 phút để hoàn thành quy trình này.
![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.8.createekscluster.png?pc=60pt)

3. Liệt kê tất cả các node trong cluster.
```
kubectl get nodes
```
![Tạo EKS Cluster](../../../images/2.prerequisites/2.4.createekscluster/2.4.9.createekscluster.png?pc=60pt)


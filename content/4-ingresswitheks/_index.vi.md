---
title : "Dịch vụ Ingress với Amazon EKS Cluster"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

### Tổng quan
Khi bạn tạo một Kubernetes Ingress, một AWS Application Load Balancer (ALB) sẽ được cung cập để cân bằng lưu lượng truy cập ứng dụng, ALBs có thể được sử dụng với Pod mà được triển khai trên Node hoặc trên AWS Fargte. Bạn có thể triển khai một ALB trên public hoặc private subnet.


![Dịch vụ Ingress với Amazon EKS Cluster](../../images/4.ingresswitheks/eksalb.png?pc=60pt)



### Nội dung

+ 4.1 [Cài đặt AWS Load Balancer Controller](../../4-ingresswitheks/4.1-installlbc/)
+ 4.2 [Tạo Ingress cơ bản](../../4-ingresswitheks/4.2-basciingress/)
+ 4.3 [Context Path Based Routing Ingress trên EC2 Managed NodeGroup](../../4-ingresswitheks/4.3-conextbasedroutingingress/)
+ 4.4 [Context Path Based Routing Ingress trên Fargate Profile](../../4-ingresswitheks/4.4-ingresswithfargate/)
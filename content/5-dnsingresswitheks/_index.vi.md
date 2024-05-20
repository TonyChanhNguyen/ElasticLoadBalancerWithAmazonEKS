---
title : "(Tùy chọn) Tích hợp dịch vụ ExternalDNS cho Amazon EKS Cluster"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
### Tổng quan
Ở phần này, chúng ta sẽ khám phá làm thế nào để tích hợp ExternalDNS Service (Route53 và AWS Certificate Manager) trên Amazon EKS Cluster.
Đây là một phần tự chọn, bởi vì nó yêu cầu bạn cần có một Host Zone được kết nối bên trong Route53, Nếu bạn vẫn chưa có, bạn có thể mua một tên miền và thêm nó vào Hos Zone hoặc chỉ cần xem qua chương này để có cái nhìn tổng quan.

![Deploy Load Balancer Service on Amazon EKS](../../images/5.externaldns/eksalbdnsacm.png?pc=60pt)

### Nội dung
+ 5.1 [Triển khai ExternalDNS Service](../../5-dnsingresswitheks/5.1-deployexternaldns/)
+ 5.2 [Sử dụng ExternalDNS như Ingress Service](../../5-dnsingresswitheks/5.2-useexternaldnsasingress/)
+ 5.3 [Name Based Virtual Host Routing](../../5-dnsingresswitheks/5.3-namebasedvirtualhostrouting/)
+ 5.4 [SSL và SSL Redirect](../../5-dnsingresswitheks/5.4-sslandssldirect/)
+ 5.5 [Target Type IP](../../5-dnsingresswitheks/5.5-targettypeip/)
---
title : "Triển khai CSDL trên Amazon EKS thông qua Amazon RDS"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
### Tổng quan

Trong phần này, chúng ta sẽ tạo một máy chủ Amazon RDS. Sau đó tích hợp nó lên EKS Cluster thông qua dịch vụ ExternalName.

![Triển khai CSDL trên Amazon EKS thông qua Amazon RDS](../../images/4.eksdbwithrds/eksrdsmysql.png?pc=60pt)

### Nội dung

+ [4.1 Tạo máy chủ Amazon RDS](../4-eksdbwithrds/4.1-createrdsdb/)
+ [4.2 Triển khai ứng dụng](../4-eksdbwithrds/4.2-deployapp/)
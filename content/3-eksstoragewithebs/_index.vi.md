---
title : "Bộ lưu trữ cố định Amazon EKS với Amazon EBS"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

### Tổng quan
[Amazon Elastic Block Store](https://aws.amazon.com/ebs/) là một dịch vụ lưu trữ dạng khối hiệu năng cao, có khả năng mở rộng, dễ dàng sử dụng. Nó cũng cấp vùng lưu trữ liên tục đến người dùng. Bộ lưu trữ liên tục cho phép người dùng lưu trữ dữ liệu của họ cho đến khi họ quyết định xóa các dữ liệu đó.

![Amazon EKS Persistent Storage với Amazon EBS](../../images/3.eksstoragewithebs/eksebs.png?pc=90pt)
### Nội dung
+ [3.1 Cài đặt EBS CSI Driver](../3-eksstoragewithebs/3.1-installcsidriver/)
+ [3.2 Cung cấp tĩnh](../3-eksstoragewithebs/3.2-staticprovision/)
+ [3.3 Cung cấp động](../3-eksstoragewithebs/3.3-dynamicprovision/)
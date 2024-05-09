---
title : "Bộ lưu trữ cố định Amazon EKS với Amazon EFS"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

### Overview
[Amazon Elastic File System](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html) là một hệ thống tệp đàn hồi đơn giản, không có máy chủ, có thể cài đặt và quên để sử dụng với các dịch vụ Đám mây AWS và tài nguyên tại chỗ. Nó được xây dựng để mở rộng quy mô theo yêu cầu lên tới petabyte mà không làm gián đoạn ứng dụng, tự động tăng và thu nhỏ khi bạn thêm và xóa tệp, loại bỏ nhu cầu cung cấp và quản lý dung lượng để đáp ứng sự tăng trưởng.

![Bộ lưu trữ cố định Amazon EKS với Amazon EFS](../../images/4.eksstoragewithefs/eksefs.png?pc=60pt)

### Content
+ [4.1 Cài đặt EFS CSI Driver](../4-eksstoragewithefs/4.1-installecsidriver/)
+ [4.2 Tạo EFS File System](../4-eksstoragewithefs/4.2-createefs/)
+ [4.3 Cung cấp tĩnh](../4-eksstoragewithefs/4.3-staticprovision/)
+ [4.4 Cung cấp động](../4-eksstoragewithefs/4.4-dynamicprovision/)
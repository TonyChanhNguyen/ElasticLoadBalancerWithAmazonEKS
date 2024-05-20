---
title : "Giới thiệu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
### Tổng quan

[Classic Load Balancer](https://aws.amazon.com/elasticloadbalancing/classic-load-balancer/) cung cấp khả năng cân bằng tải đơn giản trên nhiều phiên bản Amazon EC2 và hoạt động tại cả cấp độ yêu cầu và cấp độ kết nối. Classic Load Balancer được thiết kế dành cho các ứng dụng được xây dụng với mạng EC2-Classic.

[Application Load Balancer](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/) hoạt động ở cấp độ yêu cầu (lớp layer 7), điều hướng truy cập đến các mục tiêu (máy chủ EC2, container, địa chỉ IP, và Lambda) dựa trên nội dung của yêu cầu. Lý tưởng cho khả năng cân bằng tải nâng cao của các truy cập HTTP và HTTPS, Application Load Balancer cung cấp tính năng định tuyến yêu cầu nâng cao nhằm phân phối các kiến trúc ứng dụng hiện đại, bao gồm các ứng dụng microservice và container-based. Application Load Balancer đơn giản hóa và nâng cao khả năng bảo mật của ứng dụng, bằng việc đảm bảo rằng các giao thức và mật mã SSL/TLS mới nhất luôn được sử dụng.

[Network Load Balancer](https://aws.amazon.com/elasticloadbalancing/network-load-balancer/) hoạt động ở cấp độ kết nối (lớp layer 4), điều hướng kết nối đến mục tiêu (máy chủ Amazon EC2, microservice và container) bên trong Amazon VPC, dựa vào dữ liệu phương thức IP. Lý tưởng cho khả năng cân bằng tải cho cả lưu lượng truy cập TCP và UDP, Network Load Balancer có khả năng xử lý hàng triệu yêu cầu mỗi giây trong khi vẫn duy trì độ trễ cực thấp. Network Load Balancer được tối ưu hóa để xử lý các dạng lưu lượng truy cập đột ngột và không ổn định trong khi vẫn sử dụng một địa chỉ IP tĩnh mỗi Availability Zone. Nó được tích hợp với các dịch vụ phổ biến khác của AWS như Auto Scaling, Amazon EC2 Container Service (ECS), Amazon CloudFormation, và AWS Certificate Manager (ACM).


[Route 53](https://aws.amazon.com/route53/) là một dịch vụ web Hệ thống tên miền (DNS) có tính sẵn sàng cao và có khả năng mở rộng. Route 53 kết nối yêu cầu của người dùng tới các ứng dụng internet chạy trên AWS hoặc tại chỗ (on-premise).

[AWS Certificate Manager](https://aws.amazon.com/certificate-manager/) giúp cung cấp, quản lý, và triển khai chứng chỉ SSL/TLS công khai và riêng tư để sử dụng với các dịch vụ của AWS và các tài nguyên được kết nối nội bộ. ACM loại bỏ các quy trình thủ công tốn thời gian như chi trả, tải lên, và làm mới các chứng chỉ SSL/TLS.

![Triển khai dịch vụ cân bằng tải trên Amazon EKS](../../images/eksingress.png?pc=60pt)

### Nội dung

1. [Giới thiệu](../1-introduce/)
2. [Các bước chuẩn bị](../2-prerequiste/)
3. [Dịch vụ Classic Load Balancer với Amazon EKS Cluster](../3-clbnlbwitheks/)
4. [Dịch vụ Ingress với Amazon EKS Cluster](../4-ingresswitheks/)
5. [(Tùy chọn) Tích hợp dịch vụ ExternalDNS cho Amazon EKS Cluster](../5-dnsingresswitheks/)
6. [Dịch vụ Network Load Balancer với Amazon EKS Cluster](../6-nlbwitheks/)
7. [Dọn dẹp tài nguyên](../7-cleanup/)
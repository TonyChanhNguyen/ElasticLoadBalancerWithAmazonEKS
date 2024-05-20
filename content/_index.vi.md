---
title : "Triển khai dịch vụ cân bằng tải trên Amazon EKS"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Triển khai dịch vụ cân bằng tải trên Amazon EKS

### Tổng quan

This workshop will provide a high level overview on how to integrate Load Balancer Service for application that run on Amazon EKS Cluster with Class Load Balancer, Application Load Balancer (Ingress Service) and Network Load Balancer.

Bài thực hành này sẽ cung cấp một cái nhìn tổng quan về việc làm thế nào để tích hợp dịch vụ cân bằng tải cho ứng dụng chạy trên Amazon EKS Cluster với Class Load Balancer, Application Load Balancer (Ingress Service) và Network Load Balancer.

Hơn nữa, chúng ta sẽ tích hợp dịch vụ cân bằng tải với dịch vụ quản lý tên miền (DNS) thông qua Route 53 và bảo mật truy cập vào ứng dụng bằng AWS Certificate Manager.

![Triển khai dịch vụ cân bằng tải trên Amazon EKS](../images/eksingress.png?pc=60pt)

### Nội dung

1. [Giới thiệu](../1-introduce/)
2. [Các bước chuẩn bị](../2-prerequiste/)
3. [Dịch vụ Classic Load Balancer với Amazon EKS Cluster](../3-clbnlbwitheks/)
4. [Dịch vụ Ingress với Amazon EKS Cluster](../4-ingresswitheks/)
5. [(Tùy chọn) Tích hợp dịch vụ ExternalDNS cho Amazon EKS Cluster](../5-dnsingresswitheks/)
6. [Dịch vụ Network Load Balancer với Amazon EKS Cluster](../6-nlbwitheks/)
7. [Dọn dẹp tài nguyên](../7-cleanup/)
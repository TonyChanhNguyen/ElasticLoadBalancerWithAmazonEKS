---
title : "Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 6.2 </b> "
---

## Triển khai dịch vụ Network Load Balancer cơ bản với Amazon EKS Cluster Fargate Node
#### Chỉnh sửa tệp Manifest.
Bởi vì Fargate Profile đã được tạo ở phần trước, nên chúng ta sẽ sử dụng lại nó. Hãy thêm **labels** là **runon: Fargate** cho tệp manifest ứng dụng.
1. Mở tệp tên **app-deployment.yaml** bên trong **nlb/ManagedNodeGroup**. Thêm **labels** là **runon: Fargate** tại thông số **metadata.labels** và **spec.template.metadata.labels**. Sau đó lưu lại.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.1.nlbwitheksfargatenode.png?pc=90pt)

2. Mở tệp **NetworkLoadBalancer.yaml** bên trong **nlb/ManagedNodeGroup**. Thêm **labels** là **runon: Fargate** tại thông số **metadata**. Thêm đó, chuyển thành ghi chú định nghĩa **External DNS** ở dòng **26**. Sau đó lưu lại. 
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.2.nlbwitheksfargatenode.png?pc=90pt)

#### Triển khai tài nguyên.
1. Triển khai tài nguyên trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.3.nlbwitheksfargatenode.png?pc=90pt)

2. Liệt kê các tài nguyên trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl get all -n fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.4.nlbwitheksfargatenode.png?pc=90pt)
3. Liệt kê tất cả Node trong Cluster.
```
kubectl get node -o wide
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.5.nlbwitheksfargatenode.png?pc=90pt)

Có một Fargate Instance được tạo ra cho ứng dụng.

4. Hãy khám phá Pod để xác minh rằng nó chạy trên Fargate Instance. Thay thế với Pod ID và xác minh rằng  **IP** của Pod khớp với **INTERNAL-IP** của Fargate Instance và **Node** của Pod là tên của Fargate Instance.
```
kubectl describe pod <POD-ID> -n fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.7.nlbwitheksfargatenode.png?pc=90pt)


5. Truy cập  [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để xác minh Network Load Balancer được tạo.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.6.nlbwitheksfargatenode.png?pc=90pt)


#### Kiểm tra tài nguyên.
1. Truy cập URL của ứng dụng ```http://<YOUR-NETWORK-LOAD-BALANCER-URL>:8080```.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.8.nlbwitheksfargatenode.png?pc=90pt)

Ứng dụng hoạt động!

## (Optional) Integrate with ExternalDNS service.
#### Modify Manifest file.
1. Mở tệp **NetworkLoadBalancer.yaml**, bỏ chú thích định nghĩa ExternalDNS ở dòng 26 và thay thế với ```fcjnlbfargate.<YOUR-DOMAIN-NAME>```. Sau đó lưu lại.

![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.9.nlbwitheksfargatenode.png?pc=90pt)

#### Triển khai lại tài nguyên.
1. Thực thi câu lệnh bên dưới để triển khai lại tài nguyên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.10.nlbwitheksfargatenode.png?pc=90pt)
Service tên **fcj-basic-nlb-service** sẽ được cấu hình.

#### Kiểm tra kết quả.
1. Truy cập ```http://fcjnlbfargate.<YOUR-DOMAIN-NAME>:8080``` để xác minh kết quả.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.11.nlbwitheksfargatenode.png?pc=90pt)


## Dọn dẹp
#### Xóa các tài nguyên đã tạo.
1. Xóa các tài nguyên đã tạo trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl delete -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.12.nlbwitheksfargatenode.png?pc=90pt)

#### Xóa ExternalDNS Service
1. Xóa ExternalDNS Service trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl delete -f ingress/externaldns/externaldns-deployment -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.13.nlbwitheksfargatenode.png?pc=90pt)

### Xóa Namespace đã tạo.
1. Xóa Namespace đã tạo **fcj-external-dns-ingress-ns**.
```
kubectl delete ns fcj-external-dns-ingress-ns
kubectl get ns fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster Fargate Node](../../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.14.nlbwitheksfargatenode.png?pc=90pt)

#### Chúc mừng, bạn đã triển khai ứng dụng lên Amazon EKS Cluster trên Fargate Node với Ingress và ExternalDNS Service thành công.
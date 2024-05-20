---
title : "Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 6.1 </b> "
---

## Triển khai dịch vụ Network Load Balancer cơ bản với Amazon EKS Cluster EC2 Managed Node
#### Tạo tệp Manifest.
1. Tạo thư mục làm việc mới cho phần này.
```
mkdir nlb
ls
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.1.nlbwitheksmanagednode.png?pc=90pt)

2. Tạo thư mục mới.
```
mkdir nlb/ManagedNodeGroup
ls nlb
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.2.nlbwitheksmanagednode.png?pc=90pt)

3. Sao chép tệp manifest tên **app-deployment.yaml** từ **clb/ManagedNodeGroup/** đến **nlb/ManagedNodeGroup**.
```
cp clb/ManagedNodeGroup/app-deployment.yaml nlb/ManagedNodeGroup
ls nlb/ManagedNodeGroup
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.3.nlbwitheksmanagednode.png?pc=90pt)

4. Tạo tệp tên **NetworkLoadBalancer.yaml** bên trong **nlb/ManagedNodeGroup**.
```
touch nlb/ManagedNodeGroup/NetworkLoadBalancer.yaml
ls nlb/ManagedNodeGroup
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.4.nlbwitheksmanagednode.png?pc=90pt)

5. Mở tệp **NetworkLoadBalancer.yaml**, dán đoạn code bên dưới và lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-basic-nlb-service
  annotations:
    # Traffic Routing
    service.beta.kubernetes.io/aws-load-balancer-name: fcj-basic-nlb
    service.beta.kubernetes.io/aws-load-balancer-type: external
    
    # Health Check Settings
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-protocol: http
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-port: traffic-port
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-path: /
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-healthy-threshold: "3"
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-unhealthy-threshold: "3"
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-interval: "10" 

    # Access Control
    service.beta.kubernetes.io/load-balancer-source-ranges: 0.0.0.0/0 
    service.beta.kubernetes.io/aws-load-balancer-scheme: "internet-facing"

spec:
  type: LoadBalancer
  selector:
    app: fcj-app1
  ports:
    - port: 80
      targetPort: 80
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.5.nlbwitheksmanagednode.png?pc=90pt)


#### Triển khai tài nguyên.
1. Thực thi câu lệnh bên dưới để triển khai tài nguyên trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```

![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.6.nlbwitheksmanagednode.png?pc=90pt)

Các tài nguyên được tạo:
+ Một Deployment tên **fcj-app1-deployment** với **replicas** là **1**.
+ Một ReplicaSet tên **fcj-app1-deployment-xxxxxxxxxx**  với **replicas** là **1**.
+ Một Pod tên **fcj-app1-deployment-xxxxxxxxxx-xxxxx** với **STATUS** là **Running**.
+ Một Service tên **fcj-basic-nlb-service** với **Type** là **LoadBalancer** và **EXTERNAL-IP** là **fcj-basic-nlb-b85e5b7a2c14c0d3.elb.ap-southeast-1.amazonaws.com**.

2. Hãy truy cập [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3). Có một Load Balancer được tạo với **Type** là **network** và **DNS name** khớp với **EXTERNAL-IP** của **fcj-basic-nlb-service** Service.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.7.nlbwitheksmanagednode.png?pc=90pt)

3. Nhấn vào Load Balancer để xác minh **Listeners**. Khi truy cập vào ứng dụng thông qua **TCP:8080**, nó sẽ được chuyển hướng đến **TargetGroup** được tạo.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.8.nlbwitheksmanagednode.png?pc=90pt)

4. Nhấn vào **TargetGroup** được tạo để xác minh **Registered targets**.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.9.nlbwitheksmanagednode.png?pc=90pt)

**Registered targets** là EC2 Managed Node trong Cluster với **Health status** là **Healthy**.

#### Kiểm tra kết quả.
1. Truy cập vào URL ứng dụng ```http://<YOUR-NETWORK-LOAD-BALANCER-URL>:8080```.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.10.nlbwitheksmanagednode.png?pc=90pt)


## (Tùy chọn) Tích hợp với dịch vụ ExternalDNS.
#### Chỉnh sửa tệp Manifest.
1. Mở tệp **NetworkLoadBalancer.yaml**, thêm đoạn chú thích bên dưới để tích hợp với dịch vụ ExternalDNS. Sau đó lưu lại.
```
# External DNS - For creating a Record Set in Route53
external-dns.alpha.kubernetes.io/hostname: fcjnlb.<YOUR-DOMAIN-NAME>
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.11.nlbwitheksmanagednode.png?pc=90pt)

#### Triển khai tài nguyên.
1. Thực thi câu lệnh bên dưới để triển khai tài nguyên trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.12.nlbwitheksmanagednode.png?pc=90pt)
Service tên **fcj-basic-nlb-service** sẽ được cấu hình.

#### Kiểm tra kết quả.
1. Truy cập ```http://fcjnlb.<YOUR-DOMAIN-NAME>:8080``` để kiểm tra.

![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.13.nlbwitheksmanagednode.png?pc=90pt)

#### Dọn dẹp.
1. Xóa tài nguyên đã tạo.
```
kubectl delete -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.14.nlbwitheksmanagednode.png?pc=90pt)

Sẽ giữ lại các tài nguyên liên quan đến dịch vụ **external-dns** và **SSL certificate** trên **AWS Certificate Manager** cho các phần tiếp theo.

2. Truy cập  [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để xác minh rằng Network Load Balancer được tạo ra đã được tự động xóa khi Service **fcj-basic-nlb-service** bị xóa.
![Dịch vụ Network Load Balancer với Amazon EKS Cluster EC2 Managed Node](../../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.15.nlbwitheksmanagednode.png?pc=90pt)
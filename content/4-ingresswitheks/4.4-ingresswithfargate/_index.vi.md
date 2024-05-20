---
title : "Context Path Based Routing Ingress trên Fargate Profile"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4.4 </b> "
---

Ở phần trước, chúng ta đã triển khai 2 ứng dụng trên kubernetes với định tuyến dựa trên đường dẫn với Ingress Controller trên EC2 Managed Node. Ở phần này, chúng ta sẽ triển khai chúng trên Fargate Profile.


### Tạo tệp Manifest
1. Tạo thư mục làm việc mới cho phần này.
```
mkdir ingress/context-based-routing-ingress-on-fargate
```

![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.1.contextbasedroutingingressonfargate.png?pc=90pt)

2. Tạo tệp manifest Fargate Profile.
```
mkdir ingress/context-based-routing-ingress-on-fargate/fargate-profile
ls ingress/context-based-routing-ingress-on-fargate
touch ingress/context-based-routing-ingress-on-fargate/fargate-profile/fargate-profile.yaml
ls ingress/context-based-routing-ingress-on-fargate/fargate-profile
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.2.contextbasedroutingingressonfargate.png?pc=90pt)

3. Mở tệp **fargate-profile.yaml**, dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
    name: fcj-elb-cluster
    region: ap-southeast-1
spec:
    - name: fcj-fp
      selectors:
        - namespace: fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.3.contextbasedroutingingressonfargate.png?pc=90pt)

4. Tạo một thư mục mới để chưa tệp manifest ứng dụng.
```
mkdir ingress/context-based-routing-ingress-on-fargate/application
ls ingress/context-based-routing-ingress-on-fargate
```

![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.4.contextbasedroutingingressonfargate.png?pc=90pt)

5. Sao chép tất cả tệp manifest ứng dụng ở phần trước bên trong **ingress/context-based-routing-ingress** đến **ingress/context-based-routing-ingress-on-fargate/application**.
```
cp ingress/context-based-routing-ingress/* ingress/context-based-routing-ingress-on-fargate/application
ls ingress/context-based-routing-ingress-on-fargate/application
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.5.contextbasedroutingingressonfargate.png?pc=90pt)


### Triển khai tài nguyên
#### Tạo Namespace để triển khai tài nguyên.
1. Tạo Namespace tên **fcj-context-path-based-routing-fargate-ns**, khớp với giá trị thông số **spec.selectors.namespace** trong **ingress/context-based-routing-ingress-on-fargate/fargate-profile/fargate-profile.yaml**.
```
kubectl create ns fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.6.contextbasedroutingingressonfargate.png?pc=90pt)

2. Liệt kê Namespace đã tạo.
```
kubectl get ns fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.7.contextbasedroutingingressonfargate.png?pc=90pt)

#### Tạo Fargate Profile.
1. Liệt kê Fargate Profile trong EKS Cluster.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.8.contextbasedroutingingressonfargate.png?pc=90pt)

2. Tạo Fargate Profile bên trong EKS Cluster.
```
eksctl create fargateprofile -f ingress/context-based-routing-ingress-on-fargate/fargate-profile/fargate-profile.yaml
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.9.contextbasedroutingingressonfargate.png?pc=90pt)

3. Liệt kê lại Fargate Profile trong EKS Cluster.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1
```

![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.10.contextbasedroutingingressonfargate.png?pc=90pt)

#### Triển khai tài nguyên.
1. Thực thi câu lệnh bên dưới để triển khai tài nguyên lên Namespace **fcj-context-path-based-routing-fargate-ns**.
```
kubectl apply -f ingress/context-based-routing-ingress-on-fargate/application -n fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.11.contextbasedroutingingressonfargate.png?pc=90pt)

2. Liệt kê tài nguyên đã tạo.
```
kubectl get ingress,svc,deploy,pod -n fcj-context-path-based-routing-fargate-ns
```

![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.12.contextbasedroutingingressonfargate.png?pc=90pt)

Các tài nguyên đã tạo:
+ Một Ingress tên **fcj-context-based-routing-ingress** với **ADDRESS** là **fcj-context-based-routing-427025104.ap-southeast-1.elb.amazonaws.com**.
+ Hai Service tên **fcj-app1-nodeport-service** và **fcj-app2-nodeport-service** cho ứng dụng App1 và App2.
+ Hai Deploymet tên **fcj-app1-deployment** và **fcj-app2-deployment**, và hai Pod cho mỗi ứng dụng.

3. Hãy liệt kê Node hiện tại để xác minh rằng Fargate Node đã được tạo.
```
kubectl get node -o wide
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.13.contextbasedroutingingressonfargate.png?pc=90pt)

Có hai Fargate Node được tạo.

#### Kiểm tra kết quả.
1. Truy cập ```http://<REPLACE-WITH-INGRESS-ADDRESS>/app1``` để kiểm tra kết quả.

![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.14.contextbasedroutingingressonfargate.png?pc=90pt)


2. Truy cập ```http://<REPLACE-WITH-INGRESS-ADDRESS>/app2``` để kiểm tra kết quả.

![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.15.contextbasedroutingingressonfargate.png?pc=90pt)


#### Chúc mừng, bạn đã triển khai ứng dụng với Ingress Service lên Fargate Node thành công.

### Dọn dẹp.
1. Thực thi câu lệnh dưới để xóa tài nguyên đã tạo.
```
kubectl delete -f ingress/context-based-routing-ingress-on-fargate/application -n fcj-context-path-based-routing-fargate-ns
kubectl get ingress,svc,deploy,pod -n fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.16.contextbasedroutingingressonfargate.png?pc=90pt)

Tất cả đã được xóa.

2. Xóa Fargate Profile.
```
eksctl delete fargateprofile --name fcj-fp --cluster fcj-elb-cluster --region ap-southeast-1 --wait
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.17.contextbasedroutingingressonfargate.png?pc=90pt)

3. Xóa Namespace đã tạo.
```
kubectl delete ns fcj-context-path-based-routing-fargate-ns
kubectl get ns fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.18.contextbasedroutingingressonfargate.png?pc=90pt)


4. Xác minh rằng Fargate Node đã được xóa.
```
kubectl get node -o wide
```
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.19.contextbasedroutingingressonfargate.png?pc=90pt)

Chỉ còn EC2 Managed Node.

5. Đi đến [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để xác minh rằng Application Load Balancer được xóa tự động khi Ingress Service bị xóa.
![Context Path Based Routing Ingress trên Fargate Profile](../../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.20.contextbasedroutingingressonfargate.png?pc=90pt)

---
title : "Tạo Ingress cơ bản"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

Ở phần trước, bạn đã cài đặt AWS Load Balancer Controller và tạo một Ingress Class. Trong phần này, chúng ta sẽ tạo một Ingress cơ bản để tích hợp với ứng dụng chúng ta.

### Tạo tệp Manifest
1. Tạo một thư mục làm việc cho phần này.
```
mkdir ingress/basic-ingress
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.1.basicingress.png?pc=90pt)


2. Tạo một tệp manifest tên **01-App1-Deployment.yaml** để triển khai ứng dụng.
```
touch ingress/basic-ingress/01-App1-Deployment.yaml
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.2.basicingress.png?pc=90pt)

3.Mở tệp **01-App1-Deployment.yaml**, dán đoạn code bên dưới. Sau đó lưu lại. Đừng quên thay thế với Container Image URL hoặc có thể sử dụng ```firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v1```cho mục đích kiểm thử.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fcj-app1-deployment
  labels:
    app: fcj-app1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fcj-app1
  template:
    metadata:
      labels:
        app: fcj-app1
    spec:
      containers:
        - name: fcj-app1
          image: <REPLACE-WITH-YOUR-CONTAINER-IMAGE-URL>
          ports:
            - containerPort: 8080
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.3.basicingress.png?pc=90pt)

4. Tạo tệp tên **02-NodePort-svc.yaml** để tạo NodePort cho Application Pod.
```
touch ingress/basic-ingress/02-NodePort-svc.yaml
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.4.basicingress.png?pc=90pt)

5. Mở tệp **02-NodePort-svc.yaml**, dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app1-nodeport-service
  labels:
    app: fcj-app1
spec:
  type: NodePort
  selector:
    app: fcj-app1
  ports:
    - port: 8080
      targetPort: 8080
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.5.basicingress.png?pc=90pt)

6. Tạo tệp tên **03-ALB-ingress.yaml** để tạo và tích hợp ALB Ingress Service cho ứng dụng của bạn.
```
touch ingress/basic-ingress/03-ALB-ingress.yaml
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.6.basicingress.png?pc=90pt)

7.Mở tệp **03-ALB-ingress.yaml**, dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fcj-app1-ingress
  labels:
    app: fcj-app1
  annotations:
    # Load Balancer Name
    alb.ingress.kubernetes.io/load-balancer-name: app1ingressrules
    # Ingress Core Settings
    alb.ingress.kubernetes.io/scheme: internet-facing
    # Health Check Settings
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
    alb.ingress.kubernetes.io/healthcheck-path: / 
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
    alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
    alb.ingress.kubernetes.io/success-codes: '200'
    alb.ingress.kubernetes.io/healthy-threshold-count: '2'
    alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'
spec:
  ingressClassName: my-aws-ingress-class # Ingress Class
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: fcj-app1-nodeport-service
            port:
              number: 8080
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.7.basicingress.png?pc=90pt)


### Triển khai tài nguyên.
1. Đầu tiên, tạo Namespace mới tên **fcj-basic-ingress-ns** để triển khai tài nguyên cho phần này.
```
kubectl create ns fcj-basic-ingress-ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.8.basicingress.png?pc=90pt)

2. Liệt kê Namespace đã tạo.
```
kubectl get ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.9.basicingress.png?pc=90pt)

3. Liệt kê tất cả tài nguyên trong Namespace **fcj-basic-ingress-ns** để đảm bảo rằng chưa có tài nguyên nào được tạo.
```
kubectl get ingress,deploy,svc,pod -n fcj-basic-ingress-ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.10.basicingress.png?pc=90pt)

4. Đi đến [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để kiểm tra chưa có ALB nào được tạo.

![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.11.basicingress.png?pc=90pt)

5. Bây giờ, hãy tạo tài nguyên trong Namespace **fcj-basic-ingress-ns**.
```
kubectl apply -f ingress/basic-ingress -n fcj-basic-ingress-ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.12.basicingress.png?pc=90pt)

6. Liệt kê tài nguyên được tạo.
```
kubectl get ingress,deploy,svc,pod -n fcj-basic-ingress-ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.13.basicingress.png?pc=90pt)

Các tài nguyên được tạo:
+ Một Ingress tên **fcj-app1-ingress** với **Class** là **my-aws-ingress-class**, **ADDRESS** là **app1ingressrules-2077624863.ap-southeast-1.elb.amazonaws.com** và **PORTS** là **80**.
+ Một Deployment tên **fcj-app1-deployment**.
+ Một Service tên **fcj-app1-nodeport-service** với **TYPE** f **NodePort**.
+ Một Pod tên **fcj-app1-deployment-xxxxxxxxxx-xxxxx**.

7. Đi đến [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để kiểm tra kết quả.
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.14.basicingress.png?pc=90pt)

Application Load Balancer tên **fcj-app1-ingress**, với **Type** là **application**, được tạo tự động khi Ingress được tạo.

8. Chờ đến khi **State** của Application Load Balancer tên **fcj-app1-ingress** đổi thành **Active**. Nhấn vào nó.

![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.15.basicingress.png?pc=90pt)

9. Có một **Listener**. Nhấn vào.
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.16.basicingress.png?pc=90pt)

10. Có một **Target Group** được định tuyến bởi **Path Pattern /**. Nhấn vào.
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.17.basicingress.png?pc=90pt)

11. Ứng dụng được triển khai trên EC2 Instance của Node Group với **Port number** và **Health status** là **Healthy**.
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.18.basicingress.png?pc=90pt)


12. Hãy kiểm tra kết quả bằng việc truy cập vào URL ứng dụng ```http://<REPLACE-WITH-YOUR-INGRESS-ADDRESS>```.

![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.19.basicingress.png?pc=90pt)

#### Chúc mừng, bạn đã triển khai ứng dụng của bạn và tích hợp với Ingress (AWS Application Load Balancer) Service thành công.

### Dọn dẹp.
1. Thực thi câu lệnh bên dưới để xóa tất cả tài nguyên đã tạo bên trong Namespace **fcj-basic-ingress-ns**.
```
kubectl delete -f ingress/basic-ingress -n fcj-basic-ingress-ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.20.basicingress.png?pc=90pt)

2. Liệt kê lại tài nguyên để đảm bảo rằng tất cả đã được xóa.
```
kubectl get ingress,deploy,svc,pod -n fcj-basic-ingress-ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.21.basicingress.png?pc=90pt)

3. Xóa Namespace **fcj-basic-ingress-ns**.
```
kubectl delete ns fcj-basic-ingress-ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.22.basicingress.png?pc=90pt)

4. Đảm bảo rằng nó đã được xóa.
```
kubectl get ns fcj-basic-ingress-ns
```
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.23.basicingress.png?pc=90pt)

5. Đi đến [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để xác minh rằng Application Load Balancer đã được tự động xóa khi Ingress xóa.
![Tạo Ingress cơ bản](../../../images/4.ingresswitheks/4.2.basicingress/4.2.24.basicingress.png?pc=90pt)
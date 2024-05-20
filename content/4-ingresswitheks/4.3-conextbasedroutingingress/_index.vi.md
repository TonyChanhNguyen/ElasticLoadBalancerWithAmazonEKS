---
title : "Context Path Based Routing Ingress trên EC2 Managed NodeGroup"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---

Chúng ta sẽ triển khai tất cả 2 ứng dụng trong với định tuyến dựa trên đường dẫn được kích hoạt trên Ingress Controller:
+ /app1/* - sẽ đi đến ứng dụng App1.
+ /app2/* - sẽ đi đến ứng dụng App2.

### Tạo tệp Manifest.
1. Tạo thư mục làm việc mới cho phần này.
```
mkdir ingress/context-based-routing-ingress
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.1.contextbasedroutingingress.png?pc=90pt)


2. Tạo tệp tên **01-App1-Deployment.yaml** bên trong **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/01-App1-Deployment.yaml
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.2.contextbasedroutingingress.png?pc=90pt)

3. Mở tệp **01-App1-Deployment.yaml**, dán đoạn code bên dưới. Sau đó lưu lại. Chúng ta sẽ sử dụng container image URL ```stacksimplify/kube-nginxapp1:1.0.0``` cho mục đích thử nghiệm.
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
          image: stacksimplify/kube-nginxapp1:1.0.0
          ports:
            - containerPort: 80
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.3.contextbasedroutingingress.png?pc=90pt)

4. Tạo tệp tên **02-App1-NodePort-svc.yaml** bên trong **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/02-App1-NodePort-svc.yaml
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.4.contextbasedroutingingress.png?pc=90pt)

5. Mở tệp **02-App1-NodePort-svc.yaml**, dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app1-nodeport-service
  labels:
    app: fcj-app1
  annotations:  
    alb.ingress.kubernetes.io/healthcheck-path: /app1/index.html
spec:
  type: NodePort
  selector:
    app: fcj-app1
  ports:
    - port: 80
      targetPort: 80
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.5.contextbasedroutingingress.png?pc=90pt)

6. Tạo tệp tên **03-App2-Deployment.yaml** bên trong **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/03-App1-Deployment.yaml
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.6.contextbasedroutingingress.png?pc=90pt)

7. Mở tệp **03-App2-Deployment.yaml**, dán đoạn code bên dưới. Lưu lại. Chúng ta sẽ sử dụng container image URL ```stacksimplify/kube-nginxapp2:1.0.0``` cho mục đích thử nghiệm.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fcj-app2-deployment
  labels:
    app: fcj-app2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fcj-app2
  template:
    metadata:
      labels:
        app: fcj-app2
    spec:
      containers:
        - name: fcj-app2
          image: stacksimplify/kube-nginxapp2:1.0.0
          ports:
            - containerPort: 80
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.7.contextbasedroutingingress.png?pc=90pt)


8. Tạo tệp tên **04-App2-NodePort-svc.yaml** bên trong **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/04-App2-NodePort-svc.yaml
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.8.contextbasedroutingingress.png?pc=90pt)

9. Mở tệp **04-App2-NodePort-svc.yaml**, dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app2-nodeport-service
  labels:
    app: fcj-app2
  annotations:  
    alb.ingress.kubernetes.io/healthcheck-path: /app2/index.html
spec:
  type: NodePort
  selector:
    app: fcj-app2
  ports:
    - port: 80
      targetPort: 80
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.9.contextbasedroutingingress.png?pc=90pt)

10. Tạo tệp tên **05-ALB-ingress.yaml** bên trong **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/05-ALB-ingress.yaml
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.10.contextbasedroutingingress.png?pc=90pt)

11. Mở tệp **05-ALB-ingress.yaml**, dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fcj-context-based-routing-ingress
  annotations:
    # Load Balancer Name
    alb.ingress.kubernetes.io/load-balancer-name: fcj-context-based-routing
    # Ingress Core Settings
    alb.ingress.kubernetes.io/scheme: internet-facing
    # Health Check Settings
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
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
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: fcj-app1-nodeport-service
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: fcj-app2-nodeport-service
            port:
              number: 80
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.11.contextbasedroutingingress.png?pc=90pt)

### Triển khai tài nguyên.
1. Tạo Namespace mới để triển khai tài nguyên.
```
kubectl create ns fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.12.contextbasedroutingingress.png?pc=90pt)

2. Liệt kê Namespace đã tạo.
```
kubectl get ns fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.13.contextbasedroutingingress.png?pc=90pt)

3. Triển khai tài nguyên lên Namespace đã tạo.
```
kubectl apply -f ingress/context-based-routing-ingress -n fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.14.contextbasedroutingingress.png?pc=90pt)


4. Liệt kê tài nguyên đã tạo.
```
kubectl get ingress,svc,deploy,pod -n fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.15.contextbasedroutingingress.png?pc=90pt)

Các tài nguyên được tạo:
+ Một Ingress tên **fcj-context-based-routing-ingress** với **ADDRESS** là **fcj-context-based-routing-1220596484.ap-southeast-1.elb.amazonaws.com**.
+ Hai Service tên **fcj-app1-nodeport-service** và **fcj-app2-nodeport-service** cho Application App1 và App2.
+ Hai Deploymet tên **fcj-app1-deployment** và **fcj-app2-deployment**, và 2 Pod được tạo cho mỗi Application.

5. Đi đến [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để xác minh có một Application Load Balancer được tạo tự động.
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.16.contextbasedroutingingress.png?pc=90pt)


6. Xác minh **Listener Rule** của Application Load Balancer. Có 2 quy tắc được tạo: Một cho App1 và một cho App2.
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.17.contextbasedroutingingress.png?pc=90pt)

7. Truy cập tới **Ingress Address** để kiểm tra kết quả:
+ Truy cập với ```/app1``` để đi đến Application App1.
+ Truy cập với ```/app2``` để đi đến Application App2.
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.18.contextbasedroutingingress.png?pc=90pt)
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.19.contextbasedroutingingress.png?pc=90pt)

### Dọn dẹp
1. Thực thi câu lệnh bên dưới để xóa tài nguyên trên Namespace **fcj-context-based-routing-ns**.
```
kubectl delete -f ingress/context-based-routing-ingress -n fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.20.contextbasedroutingingress.png?pc=90pt)

2. Liệt kê tài nguyên lần nữa để kiểm tra tất cả đã được xóa.
```
kubectl get ingress,svc,deploy,pod -n fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.21.contextbasedroutingingress.png?pc=90pt)

3. Xóa Namespace **fcj-context-based-routing-ns**.
```
kubectl delete ns fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.22.contextbasedroutingingress.png?pc=90pt)

4. Đảm bảo rằng nó đã được xóa.
```
kubectl get ns fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.23.contextbasedroutingingress.png?pc=90pt)

5. Đi đến [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để xác minh Application Load Balancer được xóa tự động khi Ingress bị xóa.
![Context Based Routing Ingress](../../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.24.contextbasedroutingingress.png?pc=90pt)
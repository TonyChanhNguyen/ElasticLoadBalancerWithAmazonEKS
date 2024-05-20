---
title : "Target Type IP"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

Trong phần này, chúng ta sẽ triển khai ứng dụng trên Fargate Node với Ingress và ExternalDNS Service.

### Tạo tệp Manifest.
1. Tạo thư mục mới cho phần này.
```
mkdir ingress/externaldns/target-type-ip
ls ingress/externaldns
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.1.targettypeip.png?pc=90pt)

2. Tạo thư mục **farget-profile** để lưu tệp định nghĩa fargate profile.
```
mkdir ingress/externaldns/target-type-ip/farget-profile
ls ingress/externaldns/target-type-ip
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.2.targettypeip.png?pc=90pt)

3. Tạo **fargate-profile.yaml** bên trong **ingress/externaldns/target-type-ip/farget-profile**.
```
touch ingress/externaldns/target-type-ip/farget-profile/fargate-profile.yaml
ls ingress/externaldns/target-type-ip/farget-profile/
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.3.targettypeip.png?pc=90pt)

4. Mở tệp **fargate-profile.yaml** và dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
    name: fcj-elb-cluster
    region: ap-southeast-1
fargateProfiles:
    - name: fcj-fp
      selectors:
        - namespace: fcj-external-dns-ingress-ns
          labels:
            runon: fargate
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.4.targettypeip.png?pc=90pt)

5. Tạo thư mục mới để chứa tệp manifest triển khai ứng dụng.
```
mkdir ingress/externaldns/target-type-ip/application
ls ingress/externaldns/target-type-ip
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.5.targettypeip.png?pc=90pt)

6. Tạo tệp tên **01-App1-Deployment.yaml** bên trong **ingress/externaldns/target-type-ip/application**.
```
touch ingress/externaldns/target-type-ip/application/01-App1-Deployment.yaml
ls ingress/externaldns/target-type-ip/application
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.6.targettypeip.png?pc=90pt)

7. Mở tệp **01-App1-Deployment.yaml** và dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fcj-app1-deployment
  labels:
    app: fcj-app1
    runon: fargate
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fcj-app1
  template:
    metadata:
      labels:
        app: fcj-app1
        runon: fargate 
    spec:
      containers:
        - name: fcj-app1
          image: stacksimplify/kube-nginxapp1:1.0.0
          ports:
            - containerPort: 80
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.7.targettypeip.png?pc=90pt)

8. Tạo tệp tên **02-App1-ClusterIP.yaml** bên trong **ingress/externaldns/target-type-ip/application**.
```
touch ingress/externaldns/target-type-ip/application/02-App1-ClusterIP.yaml
ls ingress/externaldns/target-type-ip/application
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.8.targettypeip.png?pc=90pt)

9. Mở tệp **02-App1-ClusterIP.yaml** và dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app1-clusterip-service
  labels:
    app: fcj-app1
    runon: fargate
  annotations:
    alb.ingress.kubernetes.io/healthcheck-path: /app1/index.html
spec:
  type: ClusterIP
  selector:
    app: fcj-app1
  ports:
    - port: 80
      targetPort: 80
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.9.targettypeip.png?pc=90pt)

10. Tương tự, tạo tệp tên **03-App2-Deployment.yaml** và dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fcj-app2-deployment
  labels:
    app: fcj-app2
    runon: fargate
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fcj-app2
  template:
    metadata:
      labels:
        app: fcj-app2
        runon: fargate
    spec:
      containers:
        - name: fcj-app2
          image: stacksimplify/kube-nginxapp2:1.0.0
          ports:
            - containerPort: 80
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.10.targettypeip.png?pc=90pt)

11. Tương tự, tạo tệp tên **04-App2-ClusterIP.yaml** và dán đoạn code bên dưới. Sau đó lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app2-clusterip-service
  labels:
    app: fcj-app2
    runon: fargate
  annotations:
    alb.ingress.kubernetes.io/healthcheck-path: /app2/index.html
spec:
  type: ClusterIP
  selector:
    app: fcj-app2
  ports:
    - port: 80
      targetPort: 80
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.11.targettypeip.png?pc=90pt)

12. Tương tự, tạo tệp tên **05-ALB-ingress.yaml**, dán đoạn code bên dưới và thay thế ```<REPLACE-WITH-YOUR-HOST-HEADER-APP1>``` và ```<REPLACE-WITH-YOUR-HOST-HEADER-APP2>``` với Host Header . Sau đó lưu lại.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fcj-target-type-ip-ingress
  labels:
    runon: fargate
  annotations:
    # Load Balancer Name
    alb.ingress.kubernetes.io/load-balancer-name: fcj-target-type-ip
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
    ## SSL Settings
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS":443}, {"HTTP":80}]'
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:ap-southeast-1:170074558790:certificate/3798d174-1dc8-4380-974b-c219a4317730
    # SSL Redirect Setting
    alb.ingress.kubernetes.io/ssl-redirect: '443'   
    # For Fargate
    alb.ingress.kubernetes.io/target-type: ip 
spec:
  ingressClassName: my-aws-ingress-class # Ingress Class
  rules:
  - host: <REPLACE-WITH-YOUR-HOST-HEADER-APP1>
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: fcj-app1-clusterip-service
            port:
              number: 80
  - host: <REPLACE-WITH-YOUR-HOST-HEADER-APP2>
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: fcj-app2-clusterip-service
            port:
              number: 80
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.12.targettypeip.png?pc=90pt)


### Triển khai tài nguyên.
#### Tạp Fargate Profile
1. Tạo Fargate Profile trong EKS Cluster.
```
eksctl create fargateprofile -f ingress/externaldns/target-type-ip/farget-profile/fargate-profile.yaml
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.13.targettypeip.png?pc=90pt)

2. Liệt kê Fargate Profile trong EKS Cluster.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.14.targettypeip.png?pc=90pt)

#### Triển khai tài nguyên.
1. Triển khai tài nguyên trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/target-type-ip/application -n fcj-external-dns-ingress-ns
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.15.targettypeip.png?pc=90pt)

2. Liệt kê các tài nguyên đã tạo.
```
kubectl get all -n fcj-external-dns-ingress-ns
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.16.targettypeip.png?pc=90pt)

3. Liệt kê tất cả Node trong Cluster.
```
kubectl get node -o wide
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.17.targettypeip.png?pc=90pt)

Có 2 máy chủ Fargate cho ứng dụng App1 và App2.

### Kiểm tra kết quả.
1. Truy cập ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.18.targettypeip.png?pc=90pt)

2. Truy cập ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2```.
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.19.targettypeip.png?pc=90pt)

### Dọn dẹp
1. Xóa các tài nguyên đã tạo.
```
kubectl delete -f ingress/externaldns/target-type-ip/application -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.20.targettypeip.png?pc=90pt)

Sẽ giữ lại các tài nguyên liên quan đến dịch vụ **external-dns** và **SSL certificate** trên **AWS Certificate Manager** cho các phần tiếp theo.


2. Liệt kê các Node hiện tại trong Cluster để xác minh hai Fargate Node được tạo đã bị xóa thành công.
```
kubectl get node -o wide
```
![Target Type IP](../../../images/5.externaldns/5.5.targettypeip/5.5.21.targettypeip.png?pc=90pt)
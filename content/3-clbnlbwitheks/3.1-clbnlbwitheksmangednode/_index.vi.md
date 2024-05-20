---
title : "Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---

### Tạo các tệp Manifest
1. Tạo một thư mục cho phần **CLB**.
```
cd ..
mkdir clb
cd clb
```

![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.1.clbnlbwitheksmangednode.png?pc=90pt)

2. Tạo một tệp tên **app-deployment.yaml** bên trong **clb**.
```
touch app-deployment.yaml
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.2.clbnlbwitheksmangednode.png?pc=90pt)

3. Mở tệp **app-deployment.yaml**, dán đoạn mã code bên dưới. Sau đó lưu lại. Đừng quên thay thế **`<REPLACE-WITH-YOUR-CONTAINER-IMAGE-URL>`** với container image URL của bạn trên DockerHub.
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
            - containerPort: 80
```
Ở trường hợp này, container image URL trên DockerHub là ```firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v1```. Bạn có thể thay thế bằng nó cho mục đích kiểm thử.

![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.3.clbnlbwitheksmangednode.png?pc=90pt)

4. Tạo tệp **ClassicLoadBalancer.yaml** bên trong **clb**.
```
touch ClassicLoadBalancer.yaml
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.4.clbnlbwitheksmangednode.png?pc=90pt)


5. Mở tệp **ClassicLoadBalancer.yaml**, dán đoạn mã code bên dưới. Sau đó lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app1-clb-svc
  labels: 
    app: fcj-app1
spec:
  type: LoadBalancer # Default - CLB
  selector:
    app: fcj-app1
  ports: 
    - port: 8080
      targetPort: 8080
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.5.clbnlbwitheksmangednode.png?pc=90pt)


### Triển khai tài nguyên
1. Tạo một namespace để triển khai tài nguyên.
```
kubectl create ns fcj-app1
kubectl get ns
```

![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.6.clbnlbwitheksmangednode.png?pc=90pt)

2. Thực thi câu lệnh bên dưới để triển khai tài nguyên lên namespace **fcj-app1**.
```
kubectl apply -n fcj-app1 -f .
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.7.clbnlbwitheksmangednode.png?pc=90pt)

3. Liệt kê tất cả tài nguyên đã tạo trên namespace **fcj-app1**.
```
kubectl get deploy,pod,svc -n fcj-app1
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.8.clbnlbwitheksmangednode.png?pc=90pt)

Các tài nguyên được tạo ở namespace **fcj-app1**:
+ Một Deployment tên **fcj-app1-deployment**.
+ Một Pod tên **fcj-app1-deployment-7d9d4f7c44-ddvj8** với **STATUS** là **Running**.
+ Một Service tên **fcj-app1-clb-svc** với **TYPE** là **LoadBalancer** và **EXTERNAL-IP** là **a89ef47f06ea949c2b6e6e91bafc6545-898842507.ap-southeast-1.elb.amazonaws.com**.

4. Hãy truy cập [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3). Có một Load Balancer được tạo với **Type** là **classic** và **DNS name** khớp với **EXTERNAL-IP** của **fcj-app1-clb-svc** Service.
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.9.clbnlbwitheksmangednode.png?pc=90pt)

5. Hãy truy cập đường dẫn ```http://<YOUR-EXTERNAL-IP>:8080``` để kiểm tra kết quả.
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.10.clbnlbwitheksmangednode.png?pc=90pt)

Ứng dụng của bạn đã được triển khai lên Classic Load Balancer thành công.

### Dọn dẹp.
1. Thực thi câu lệnh bên dưới để xóa các tài nguyên đã tạo.
```
kubectl delete -n fcj-app1 -f .
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.11.clbnlbwitheksmangednode.png?pc=90pt)

2. Liệt kê tất cả tài nguyên để đảm bảo rằng chúng đã được xóa.
```
kubectl get deploy,pod,svc -n fcj-app1
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.12.clbnlbwitheksmangednode.png?pc=90pt)

3. Xóa Namespace đã tạo.
```
kubectl delete ns fcj-app1
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.13.clbnlbwitheksmangednode.png?pc=90pt)

4. Liệt kê tất cả namespace để đảm bảo rằng nó đã được xóa.
```
kubectl get ns
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.14.clbnlbwitheksmangednode.png?pc=90pt)

5. Truy cập [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để xác minh Classic Load Balancer vừa tạo đã tự động được xóa khi **fcj-app1-clb-svc** Service bị xóa.
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster EC2 Managed NodeGroup](../../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.15.clbnlbwitheksmangednode.png?pc=90pt)
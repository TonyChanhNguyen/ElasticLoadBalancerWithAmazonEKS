---
title : "Triển khai ứng dụng"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---
Bây giờ, chúng ta sẽ tạo các tệp manifest để triển khai ứng dụng và các dịch vụ của nó

### Triển khai ứng dụng
1. Tạo một tệp tên **05-fcjmgmt-deployment.yaml**.
```
touch 05-fcjmgmt-deployment.yaml
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.1.deployappdeployment.png?pc=60pt)


2. Mở tệp **05-fcjmgmt-deployment.yaml**, dán đoạn mã code sau. Thay thế ```<YOUR-REPOSITORY-PATH:TAG>``` với tên Repository và tag của bạn. Trong trường hợp này là ```firstcloudjourneypcr/fcj-management:v1```.
```
apiVersion: apps/v1
kind: Deployment 
metadata:
  name: fcjmgmt-microservice
  labels:
    app: fcjmgmt-restapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fcjmgmt-restapp
  template:  
    metadata:
      labels: 
        app: fcjmgmt-restapp
    spec:
      containers:
        - name: fcjmgmt-restapp
          image: <YOUR-REPOSITORY-PATH:TAG>
          ports: 
            - containerPort: 8080           
          env:
            - name: DB_HOST
              value: "mysql-svc"            
            - name: DB_PORT
              value: "3306"            
            - name: DB_NAME
              value: "fcjmgmt"            
            - name: DB_USER
              value: "admin"            
            - name: DB_PASS
              value: "123Vodanhphai" 
            - name: PORT
              value: "8080"        
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.2.deployappdeployment.png?pc=60pt)
3. Tạo một tệp tên **06-fcjmgmt-svc.yaml**.
```
touch 06-fcjmgmt-svc.yaml
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.3.deployappdeployment.png?pc=60pt)

4. Mở tệp **06-fcjmgmt-svc.yaml**, dán đoạn mã code bên dưới và lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: fcjmgmt-restapp-service
  labels: 
    app: fcjmgmt-restapp
spec:
  type: NodePort
  selector:
    app: fcjmgmt-restapp
  ports: 
    - port: 8080
      targetPort: 8080
      nodePort: 31231
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.4.deployappdeployment.png?pc=60pt)

5. Hãy triển khai ứng dụng của bạn.
```
kubectl apply -f .
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.5.deployappdeployment.png?pc=60pt)

6. Liệt kê tất cả tài nguyên đã tạo.
```
kubectl get sc,pv,pvc,deploy,svc,pod
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.6.deployappdeployment.png?pc=60pt)

Có một **Service** mới được tạo tên **fcjmgmt-restapp-service** với **TYPE** là **NodePort** và **PORT** là **8080:31231/TCP**, một **Deployment** mới được tạo tên **fcjmgmt-microservice** và **Pod** với **STATUS** là **Running**.

7. Lấy **Public IP Address** của Node nơi Pod đang chạy.
```
kubectl get node -o wide
```

![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.8.deployappdeployment.png?pc=60pt)
**Public IP Address** là **EXTERNAL-IP**.

8. Truy cập đến ứng dụng URL để kiểm tra kết quả ```http://<EXTERNAL-IP>:31231```.

![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.7.deployappdeployment.png?pc=60pt)

Ứng dụng của bạn hiện chưa thể truy cập được. Điều này bởi vì **Security Group** của **Node** nơi Pod đang chạy vẫn chưa mở cổng 31231.

9. Đi đến [Security Group](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#SecurityGroups:v=3;search=:eks-cluster-sg-fcj-db-cluster)/
10. Nhấn vào Security Group tìm kiếm được.

![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.9.deployappdeployment.png?pc=60pt)

11. Nhấn **Edit inbound rules**.
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.10.deployappdeployment.png?pc=60pt)

12. Thêm một quy tắc mới:
    + **Type** là **Custom TCP**.
    + **Port range** là **31231**.
    + **Source** là **Anywhere-IPv4**.
13. Nhấn **Save rules**.
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.11.deployappdeployment.png?pc=60pt)


14. Truy cập lại URL của ứng dụng để kiểm tra kết quả.
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.12.deployappdeployment.png?pc=60pt)

Ứng dụng của bạn đã được triển khai thành công.

### Kiểm thử ứng dụng
1. Nhấn **Add New User**.
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.13.deployappdeployment.png?pc=60pt)

2. Nhập tất cả thông tin và nhấn **Submit**.

![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.14.deployappdeployment.png?pc=60pt)

3. Nhập lại bước trên để thêm nhiều User hơn.

4. Sau đó, nhấn **Home** để trở về trang chủ.

![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.15.deployappdeployment.png?pc=60pt)

5. Kiểm tra kết quả, có nhiều User đã được thêm vào ứng dụng của bạn.
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.16.deployappdeployment.png?pc=60pt)


Bây giờ chúng ta sẽ xóa Pod ứng dụng và tạo một cái mới để kiểm tra xem dữ liệu đã nhập có được giữ hay không?
6. Tại cửa sổ lệnh Cloud9, xóa Pod ứng dụng.
```
kubectl delete -f 05-fcjmgmt-deployment.yaml
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.17.deployappdeployment.png?pc=60pt)

7. Liệt kê lại tất cả tài nguyên để đảm bảo rằng Pod ứng dụng và Deployment đã được xóa.
```
kubectl get pod,deployment
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.18.deployappdeployment.png?pc=60pt)

Chúng đã bị xóa hoàn toàn.

8. Hãy tạo lại chúng.
```
kubectl apply -f 05-fcjmgmt-deployment.yaml
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.19.deployappdeployment.png?pc=60pt)

9. Liệt kê lại tất cả tài nguyên để đảm bảo rằng chúng đã được tạo.
```
kubectl get pod,deployment
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.20.deployappdeployment.png?pc=60pt)

10. Truy cập lại vào URL của ứng dụng để kiểm tra kết quả.
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.21.deployappdeployment.png?pc=60pt)



Tất cả dữ liệu vẫn được giữ trong khi Pod và Deployment bị thay thế.
### Dọn dẹp
1. Tại cửa sổ lệnh Cloud9, thực thi câu lệnh bên dưới để xóa tất cả tài nguyên đã tạo.
```
kubectl delete -f .
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.22.deployappdeployment.png?pc=60pt)

2. Liệt kê tất cả tài nguyên lại để đảm bảo rằng chúng đã được xóa hoàn toàn.
```
kubectl get sc,pv,pvc,deploy,svc,pod
```
![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.23.deployappdeployment.png?pc=60pt)
Tất cả đã được xóa hoàn toàn

3. Đi đến [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes:v=3) để xác minh EBS Volume đã được xóa.

![Triển khai ứng dụng](../../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.24.deployappdeployment.png?pc=60pt)
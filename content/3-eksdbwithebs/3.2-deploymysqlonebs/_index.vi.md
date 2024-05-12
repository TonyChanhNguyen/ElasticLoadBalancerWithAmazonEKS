---
title : "Triển khai CSDL MySQL trên EBS Volume"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---
Trong phần này, chúng ta sẽ sử dụng bộ lưu trữ cho EKS Cluster bằng việc tạo EBS Volume. Sau đó tạo một CSDL MySQL trên bộ lưu trữ này để kết nối tới ứng dụng.

### Cung cấp động EBS Volume.
1. Tạo một thư mục để lưu trữ tất cả các tệp manifest trong **fcj-user-management**.
```
cd ..
mkdir kube-manifest
cd kube-manifest
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.1.deploymysqlonebs.png?pc=60pt)

2. Tạo một tệp tên **01-storage-class.yaml**.
```
touch 01-storage-class.yaml
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.2.deploymysqlonebs.png?pc=60pt)

3. Mở tệp **01-storage-class.yaml**, dán đoạn mã code bên dưới và lưu lại.
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata: 
  name: ebs-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer 
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.3.deploymysqlonebs.png?pc=60pt)

4. Tạo một tệp tên **02-pvc.yaml**.
```
touch 02-pvc.yaml
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.4.deploymysqlonebs.png?pc=60pt)

5. Mở tệp **02-pvc.yaml**, dán đoạn mã code bên dưới và lưu lại.
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-mysql-pv-claim
spec: 
  accessModes:
    - ReadWriteOnce
  storageClassName: ebs-sc
  resources: 
    requests:
      storage: 4Gi
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.5.deploymysqlonebs.png?pc=60pt)

6. Tạo một tệp tên **03-mysql-deployment.yaml**.
```
touch 03-mysql-deployment.yaml
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.6.deploymysqlonebs.png?pc=60pt)

7. Mở tệp **03-mysql-deployment.yaml**, dán đoạn mã code bên dưới và lưu lại.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec: 
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate 
  template: 
    metadata: 
      labels: 
        app: mysql
    spec: 
      containers:
        - name: mysql
          image: mysql:5.6
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: 123Vodanhphai
          ports:
            - containerPort: 3306
              name: mysql    
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql                                          
      volumes: 
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: ebs-mysql-pv-claim
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.7.deploymysqlonebs.png?pc=60pt)

8. Tạo một tệp tên **04-mysql-clusterip.yaml**.
```
touch 04-mysql-clusterip.yaml
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.8.deploymysqlonebs.png?pc=60pt)

9. Mở tệp **04-mysql-clusterip.yaml**, dán đoạn mã code bên dưới và lưu lại.
```
apiVersion: v1
kind: Service
metadata: 
  name: mysql-svc
spec:
  selector:
    app: mysql 
  ports: 
    - port: 3306  
  clusterIP: None # This means we are going to use Pod IP    
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.9.deploymysqlonebs.png?pc=60pt)


10. Bây giờ, chúng ta sẽ thử triển khai **MySQL Deployment** để kiểm tra CSDL MySQL.
```
kubectl apply -f .
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.10.deploymysqlonebs.png?pc=60pt)
11. Liệt kê tất cả tài nguyên được tạo.
```
kubectl get sc,pv,pvc,deploy,svc,pod
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.11.deploymysqlonebs.png?pc=60pt)

Có rất nhiều tài nguyên được tạo:
+ Một **Storage Class** tên **ebs-sc**.
+ Một **Persistent Volume (PV)** với tên được tạo tự động, trạng thái là **Bound** và **Capacity** là **4Gi**.
+ Một **Persistent Volume Claim (PVC)** tên **ebs-mysql-pv-claim**, trạng thái là **Bound** và **Capacity** là **4Gi**.
+ Một Service Type **ClusterIP** tên **mysql-svc** với **Port** là **3306**.
+ Một Deployment tên **mysql** với **READY** là **1/1** (nghĩa là có 1 pod được tạo thành công với replicas là 1).
+ Một Pod tên **DeploymentName-xxxxxxx-xxxxx** với **STATUS** là **Running**.

12. Bât giờ hãy sử dụng một Ephemeral Pod để kiểm tra kết nối tới CSDL MySQL.
```
kubectl run test-mysql-ephemeral-pod -it --rm --image=mysql:5.6 -- mysql -h mysql-svc -p123Vodanhphai
```


![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.12.deploymysqlonebs.png?pc=60pt)

Ephemeral Pod của bạn đã kết nối tới CSDL MySQL thành công.
13. Liệt kê mô hình của CSDL MySQL.
```
show schemas;
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.13.deploymysqlonebs.png?pc=60pt)

14. Tạo CSDL```fcjmgmt``` cho ứng dụng.
```
CREATE DATABASE fcjmgmt;
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.14.deploymysqlonebs.png?pc=60pt)

15. Liệt kê mô hình của CSDL MySQL lại để thấy CSDL đã tạo ```fcjmgmt```.
```
show schemas;
```
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.15.deploymysqlonebs.png?pc=60pt)

16. Tạo một bảng tên ```user``` bên trong CSDL **fcjmgmt**.
```
use fcjmgmt;
CREATE TABLE `user` ( `id` INT NOT NULL AUTO_INCREMENT , `first_name` VARCHAR(45) NOT NULL , `last_name` VARCHAR(45) NOT NULL , `email` VARCHAR(45) NOT NULL , `phone` VARCHAR(45) NOT NULL , `comments` TEXT NOT NULL , `status` VARCHAR(10) NOT NULL DEFAULT 'active' , PRIMARY KEY (`id`)) ENGINE = InnoDB;
show tables;
```

![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.18.deploymysqlonebs.png?pc=60pt)


17. Nhấn ```exit``` để thoát khỏi Ephemeral pod. Ephemeral Pod nãy sẽ bị xóa sau khi bạn thoát khỏi nó.
![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.16.deploymysqlonebs.png?pc=60pt)


18. Đi đến [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes:v=3) để xác minh có một ổ đĩa được tạo với **Capacity** là **4Gi**.

![Triển khai CSDL MySQL trên EBS Volume](../../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.19.deploymysqlonebs.png?pc=60pt)

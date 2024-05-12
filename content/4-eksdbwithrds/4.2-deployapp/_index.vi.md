---
title : "Triển khai ứng dụng"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

### Tạo tệp Manifest.
1. Tạo thư mục làm việc cho chương này.
```
cd ..
mkdir eks-with-rds
ls
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.1.deployapp.png?pc=60pt)

2. Sao chép **05-fcjmgmt-deployment.yaml** và **06-fcjmgmt-svc.yaml** trong thư mục **kube-manifest**.
```
cp kube-manifest/05-fcjmgmt-deployment.yaml eks-with-rds
cp kube-manifest/06-fcjmgmt-svc.yaml eks-with-rds
ls eks-with-rds
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.2.deployapp.png?pc=60pt)

Để giúp EKS Cluster của chúng ta kết nối tới CSDL Amazon RDS đã tạo, chúng ta sẽ sử dụng dịch vụ **externalName** để tạo kết nối.

3. Tạo tệp tên **07-RDS-externalName-svc.yaml** bên trong **eks-with-rds**.
```
touch eks-with-rds/07-RDS-externalName-svc.yaml
ls eks-with-rds
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.3.deployapp.png?pc=60pt)

4. Mở tệp **07-RDS-externalName-svc.yaml**, dán đoạn mã code bên dưới và lưu lại.
```
apiVersion: v1
kind: Service
metadata:
  name: mysql-svc
spec:
  type: ExternalName
  externalName: <REPLACE-WITH-YOUR-RDS-ENDPOINT>
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.4.deployapp.png?pc=60pt)

### Triển khai tài nguyên
1. Tại cửa sổ lệnh Cloud9, thực thi câu lệnh bên dưới để triển khai tài nguyên.
```
kubectl apply -f eks-with-rds
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.5.deployapp.png?pc=60pt)

2. Liệt kê tất cả tài nguyên đã tạo.
```
kubectl get deploy,svc,pod
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.6.deployapp.png?pc=60pt)

Có một **Deployment** được tạo tên **fcjmgmt-microservice**, một **Service** được tạo tên **fcjmgmt-restapp-service** với **Type** là **NodePort** và **PORT** là **31231**, một Service được tạo tên **mysql-svc** với **Type** là **ExternalName** và **EXTERNAL-IP** là **RDS-ENDPOINT**, một Pod được tạo với **STATUS** là **Running**.
3. Lấy **Public IP Address** của Node nôi Pod ứng dụng đang chạy.
```
kubectl get node -o wide
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.7.deployapp.png?pc=90pt)

4. Truy cập đường dẫn URL ứng dụng ```http://<REPLACE-WITH-NODE'S-EXTERNAL-IP>:31231```.
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.8.deployapp.png?pc=90pt)

Ứng dụng của bạn đã được triển khai thành công.

5. Hãy thêm một vài User nữa cho ứng dụng.
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.9.deployapp.png?pc=90pt)

6. Bây giờ, hãy xóa Deployment hiện tại sau đó tạo lại một cái khác.
```
kubectl delete -f eks-with-rds/05-fcjmgmt-deployment.yaml
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.10.deployapp.png?pc=90pt)

7. Liệt kê tất cả Deployment và Pod đang chạy để đảm bảo rằng chúng đã được xóa hoàn toàn.
```
kubectl get deploy,pod
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.11.deployapp.png?pc=90pt)
8. Hãy tạo lại Deployment và Pod.
```
kubectl apply -f eks-with-rds/05-fcjmgmt-deployment.yaml
kubectl get deploy,pod
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.12.deployapp.png?pc=90pt)

9. Tải lại trang web ứng dụng của bạn.
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.13.deployapp.png?pc=90pt)

Tất cả các thông tin của user mà bạn đã thêm vẫn được giữ nguyên trong khi Deployment và Pod đã bị thay thế.

10. Hơn thế, bạn cũng có thể thực hiện các thao tác **View**, **Edit** hoặc **Delete** với thông tin user của bạn.

![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.14.deployapp.png?pc=90pt)

### Dọn dẹp
1. Xóa tất cả tài nguyên đã tạo.
```
kubectl delete -f eks-with-rds/
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.15.deployapp.png?pc=90pt)

2. Liệt kê tất cả tài nguyên và đảm bảo chúng đã bị xóa hoàn toàn.
```
kubectl get deploy,svc,pod
```
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.16.deployapp.png?pc=90pt)

Tất cả chúng đã được xóa hoàn toàn.

3. Đi đến [RDS Database](https://ap-southeast-1.console.aws.amazon.com/rds/home?region=ap-southeast-1#databases:).
4. Chọn CSDL của bạn.
5. Nhấn **Action**.
6. Nhấn **Delete**.
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.17.deployapp.png?pc=90pt)

7. Tại trang **Delete fcj-management-db-instance instance**:
+ Bỏ chọn **Create final snapshot**.
+ Bỏ chọn **Retain automated backups**.
+ Chọn **I acknowledge that upon instance deletion, automated backups, including system snapshots and point-in-time recovery, will no longer be available.**
+ Nhập ```delete me``` để xác nhận.

8. Sau đó, nhấn **Delete**.

![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.18.deployapp.png?pc=90pt)

![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.19.deployapp.png?pc=90pt)

9. Sau đó khoảng 10 phút, CSDL của bạn sẽ bị xóa.
![Triển khai ứng dụng](../../../images/4.eksdbwithrds/4.2.deployapp/4.2.20.deployapp.png?pc=90pt)
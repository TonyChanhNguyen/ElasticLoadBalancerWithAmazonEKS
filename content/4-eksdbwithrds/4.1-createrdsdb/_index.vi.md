---
title : "Tạo CSDL Amazon RDS"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---


### Tạo một Security Group cho máy chủ CSDL
1. Đi đến [Security Group](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#SecurityGroups:).
2. Nhấn **Create security group**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.1.createrdsdb.png?pc=60pt)

3. Cấu hình thông số Security Group:
+ **Security Group Name**: Nhập ```FCJ-Management-DB-SG```.
+ **Description**: Nhập ```Security Group for DB instance```.
+ **VPC**: Chọn VPC của EKS Cluster tên **eksctl-fcj-db-cluster-cluster/VPC**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.2.createrdsdb.png?pc=60pt)

4. Tạo **Inbound rules** mới với:
+ **Type**: MYSQL/Aurora.
+ **Source**: Security group của Node.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.3.createrdsdb.png?pc=60pt)

5. Sau đó, nhấn **Create security group**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.4.createrdsdb.png?pc=60pt)

6. Security Group cho máy chủ CSDL được tạo thành công.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.5.createrdsdb.png?pc=60pt)

### Tạo DB Subnet Group
1. Đi đến [RDS Subnet Groups](https://ap-southeast-1.console.aws.amazon.com/rds/home?region=ap-southeast-1#db-subnet-groups-list:).
2. Nhấn **Create DB subnet group**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.6.createrdsdb.png?pc=60pt)

3. Tại trang **Create DB subnet group**:
+ **Name**: Nhập ```FCJ-Management-Subnet-Group```.
+ **Description**: Nhập ```Subnet Group for FCJ Management```.
+ **VPC**: Chọn VPC của EKS Cluster đã tạo.
+ **Availability Zones**: Chọn tất cả AZ.
+ **Subnets**: Chọn tất cả Subnet.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.7.createrdsdb.png?pc=60pt)

4. Sau đó, nhấn **Create**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.8.createrdsdb.png?pc=60pt)

5. DB Subnet Group được tạo thành công.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.9.createrdsdb.png?pc=60pt)

### Tạo máy chủ Amazon RDS Database.
1. Đi đến [RDS Database](https://ap-southeast-1.console.aws.amazon.com/rds/home?region=ap-southeast-1#databases:).
2. Nhấn **Create database**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.10.createrdsdb.png?pc=60pt)

3. Tại mục **Choose a database creation method**, chọn **Standard create** và **MySQL** tại **Engine type**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.11.createrdsdb.png?pc=60pt)

4. Tại mục **Templates**, chọn **Free tier**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.12.createrdsdb.png?pc=60pt)

5. Kế tiếp, thực hiện cấu hình chi tiết:

+ **DB instance identifier**: Nhập ```fcj-management-db-instance```.
+ **Master user**: Nhập ```admin```
+ **Master password**: Nhập sự lựa chọn của bạn (trong bài lab này, nhập ```123Vodanhphai```)
+ **Confirm password**: Nhập lại lần nữa.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.13.createrdsdb.png?pc=60pt)


6. Cấu hình tại mục **Connectivity**:
+ **Virtual private cloud (VPC)**: Chọn VPC của EKS Cluster.
+ **DB subnet group**: Chọn DB Subnet Group đã tạo ở bước trên.
+ **Existing VPC security groups**: Chọn SG đã tạo tên ```FCJ-Management-DB-SG```.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.14.createrdsdb.png?pc=60pt)


7. Cuộn xuống đến cuối trang và nhấn **Create database**.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.15.createrdsdb.png?pc=60pt)

8. Quá trình tạo Amazon RDS Database đang được thực hiện.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.16.createrdsdb.png?pc=60pt)

9. Sẽ mất khoảng 10 phút để tạo thành công.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.17.createrdsdb.png?pc=60pt)

10. Nhấn vào CSDL vừa tạo và lưu trữ **Endpoint** của nó để sử dụng sau.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.18.createrdsdb.png?pc=60pt)

### Kiểm tra kết nối đến CSDL Amazon RDS của bạn
1. Tại cửa sổ lệnh Cloud9, Tạo một Ephemaral Pod cho việc kiểm tra kết nối đến CSDL Amazon RDS của bạn.
Đừng quên thay thế với **Amazon RDS Endpoint**.
```
kubectl run test-connect-pod -it --rm --restart=Never --image=mysql:5.6 -- mysql -h <REPLACE-YOUR-RDS-ENDPOINT> -u root -p123Vodanhphai
```
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.19.createrdsdb.png?pc=60pt)

2. Hiện cấu trúc của CSDL.
```
show schemas;
```
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.20.createrdsdb.png?pc=60pt)

3. Tạo một CSDL ```fcjmgmt``` cho úng dụng.
```
CREATE DATABASE fcjmgmt;
show schemas;
```
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.21.createrdsdb.png?pc=60pt)

4. Tạo một bảng mới tên ```user``` bên trong CSDL **fcjmgmt**.
```
use fcjmgmt;
CREATE TABLE `user` ( `id` INT NOT NULL AUTO_INCREMENT , `first_name` VARCHAR(45) NOT NULL , `last_name` VARCHAR(45) NOT NULL , `email` VARCHAR(45) NOT NULL , `phone` VARCHAR(45) NOT NULL , `comments` TEXT NOT NULL , `status` VARCHAR(10) NOT NULL DEFAULT 'active' , PRIMARY KEY (`id`)) ENGINE = InnoDB;
show tables;
```
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.22.createrdsdb.png?pc=60pt)

5. Enter ```exit``` to quit.
![Tạo Amazon RDS](../../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.23.createrdsdb.png?pc=60pt)


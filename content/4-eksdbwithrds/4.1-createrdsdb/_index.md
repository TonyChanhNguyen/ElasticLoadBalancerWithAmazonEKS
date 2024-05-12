---
title : "Create Amazon RDS Database"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---


### Create a Security Group for the Database Instance
1. Go to [Security Group](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#SecurityGroups:).
2. Click on **Create security group**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.1.createrdsdb.png?pc=60pt)

3. Configure the Security Group settings:
+ **Security Group name**: Enter ```FCJ-Management-DB-SG```.
+ **Description**: Enter ```Security Group for DB instance```.
+ **VPC**: Select VPC of EKS Cluster named **eksctl-fcj-db-cluster-cluster/VPC**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.2.createrdsdb.png?pc=60pt)

4. Add new **Inbound rules** with:
+ **Type**: MYSQL/Aurora.
+ **Source**: Node's security group.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.3.createrdsdb.png?pc=60pt)

5. Then, click on **Create security group**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.4.createrdsdb.png?pc=60pt)

6. Security Group for the Database Instance was created successfully.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.5.createrdsdb.png?pc=60pt)

### Create DB Subnet Group
1. Go to [RDS Subnet Groups](https://ap-southeast-1.console.aws.amazon.com/rds/home?region=ap-southeast-1#db-subnet-groups-list:).
2. Click on **Create DB subnet group**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.6.createrdsdb.png?pc=60pt)

3. In the **Create DB subnet group** interface:
+ **Name**: Enter ```FCJ-Management-Subnet-Group```.
+ **Description**: Enter ```Subnet Group for FCJ Management```.
+ **VPC**: Select the created VPC's EKS Cluster.
+ **Availability Zones**: Choose all AZs.
+ **Subnets**: Choose all Subnets.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.7.createrdsdb.png?pc=60pt)

4. Then, click on **Create**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.8.createrdsdb.png?pc=60pt)

5. DB Subnet Group was created successfully.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.9.createrdsdb.png?pc=60pt)

### Create Amazon RDS Database Instance.
1. Go to [RDS Database](https://ap-southeast-1.console.aws.amazon.com/rds/home?region=ap-southeast-1#databases:).
2. Click on **Create database**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.10.createrdsdb.png?pc=60pt)

3. At **Choose a database creation method** field, select **Standard create** and **MySQL** at **Engine type**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.11.createrdsdb.png?pc=60pt)

4. At **Templates** field, select **Free tier**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.12.createrdsdb.png?pc=60pt)

5. Next, make detailed settings:

+ **DB instance identifier**: Enter ```fcj-management-db-instance```.
+ **Master user**: Enter ```admin```
+ **Master password**: Enter your choice (in the lab, enter ```123Vodanhphai```)
+ **Confirm password**: Enter the password again.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.13.createrdsdb.png?pc=60pt)


6. Setting at **Connectivity** field:
+ **Virtual private cloud (VPC)**: Select VPC's EKS Cluster.
+ **DB subnet group**: Select created DB subnet group on above step.
+ **Existing VPC security groups**: Select created SG named ```FCJ-Management-DB-SG```.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.14.createrdsdb.png?pc=60pt)


7. Scroll down to the end of page and click on **Create database**.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.15.createrdsdb.png?pc=60pt)

8. Creating Amazon RDS Database is processing.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.16.createrdsdb.png?pc=60pt)

9. It will take your about 10 minutes to create successfully.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.17.createrdsdb.png?pc=60pt)

10. Click on created Database and store its **Endpoint** to use later.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.18.createrdsdb.png?pc=60pt)

### Test the connection to your Amazon RDS Database
1. At Cloud9 Terminal, create a ephemaral Pod for testing the connection to our Amazon RDS Database.
Don't forget to replace with your **Amazon RDS Endpoint**.
```
kubectl run test-connect-pod -it --rm --restart=Never --image=mysql:5.6 -- mysql -h <REPLACE-YOUR-RDS-ENDPOINT> -u root -p123Vodanhphai
```
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.19.createrdsdb.png?pc=60pt)

2. Show the schemas of database.
```
show schemas;
```
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.20.createrdsdb.png?pc=60pt)

3. Create a database ```fcjmgmt``` for your application.
```
CREATE DATABASE fcjmgmt;
show schemas;
```
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.21.createrdsdb.png?pc=60pt)

4. Create a new table name ```user``` inside database **fcjmgmt**.
```
use fcjmgmt;
CREATE TABLE `user` ( `id` INT NOT NULL AUTO_INCREMENT , `first_name` VARCHAR(45) NOT NULL , `last_name` VARCHAR(45) NOT NULL , `email` VARCHAR(45) NOT NULL , `phone` VARCHAR(45) NOT NULL , `comments` TEXT NOT NULL , `status` VARCHAR(10) NOT NULL DEFAULT 'active' , PRIMARY KEY (`id`)) ENGINE = InnoDB;
show tables;
```
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.22.createrdsdb.png?pc=60pt)

5. Enter ```exit``` to quit.
![Create RDS](../../images/4.eksdbwithrds/4.1.createrdsdb/4.1.23.createrdsdb.png?pc=60pt)


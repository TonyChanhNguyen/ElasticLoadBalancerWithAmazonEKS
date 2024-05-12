---
title : "Deploy MySQL database on EBS Volume"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

In this section, we will utilize storage for EKS Cluster by creating EBS Volume dynamically. Then create a MySQL database on this storage to connect with application.

### Create EBS Volume dynamically.
1. Create a folder to store all manifest files inside **fcj-user-management**.
```
cd ..
mkdir kube-manifest
cd kube-manifest
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.1.deploymysqlonebs.png?pc=60pt)

2. Create a file named **01-storage-class.yaml**.
```
touch 01-storage-class.yaml
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.2.deploymysqlonebs.png?pc=60pt)

3. Open **01-storage-class.yaml** file, paste the below code and save.
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata: 
  name: ebs-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer 
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.3.deploymysqlonebs.png?pc=60pt)

4. Create a file named **02-pvc.yaml**.
```
touch 02-pvc.yaml
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.4.deploymysqlonebs.png?pc=60pt)

5. Open **02-pvc.yaml** file, paste the below code and save.
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
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.5.deploymysqlonebs.png?pc=60pt)

6. Create a file named **03-mysql-deployment.yaml**.
```
touch 03-mysql-deployment.yaml
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.6.deploymysqlonebs.png?pc=60pt)

7. Open **03-mysql-deployment.yaml** file, paste the below code and save.
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
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.7.deploymysqlonebs.png?pc=60pt)

8. Create a file named **04-mysql-clusterip.yaml**.
```
touch 04-mysql-clusterip.yaml
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.8.deploymysqlonebs.png?pc=60pt)

9. Open **04-mysql-clusterip.yaml** file, paste the below code and save.
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
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.9.deploymysqlonebs.png?pc=60pt)


10. Now, we will try to deploy **MySQL Deployment** to test MySQL Database.
```
kubectl apply -f .
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.10.deploymysqlonebs.png?pc=60pt)
11. List all created resources.
```
kubectl get sc,pv,pvc,deploy,svc,pod
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.11.deploymysqlonebs.png?pc=60pt)

There are a lot of created resources:
+ A created **Storage Class** named **ebs-sc**.
+ A created **Persistent Volume (PV)** with name created automatically, status is **Bound** and **Capacity** is **4Gi**.
+ A created **Persistent Volume Claim (PVC)** named **ebs-mysql-pv-claim**, status is **Bound** nd **Capacity** is **4Gi**.
+ A created service type **ClusterIP** named **mysql-svc** with **Port** is **3306**.
+ A created deployment named **mysql** with **READY** is **1/1** (mean there is 1 pod created successful with replicas is 1).
+ A created pod named **DeploymentName-xxxxxxx-xxxxx** with **STATUS** is **Running**.

12. Now, let using an ephemeral Pod to test the connection to your MySQL database.
```
kubectl run test-mysql-ephemeral-pod -it --rm --image=mysql:5.6 -- mysql -h mysql-svc -p123Vodanhphai
```


![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.12.deploymysqlonebs.png?pc=60pt)

Your ephemeral Pod connected to  MySQL database successfully.
13. Let list the schemas of MySQL database.
```
show schemas;
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.13.deploymysqlonebs.png?pc=60pt)

14. Create a database ```fcjmgmt``` for your application.
```
CREATE DATABASE fcjmgmt;
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.14.deploymysqlonebs.png?pc=60pt)

15. Let list the schemas of MySQL database again to see the created database ```fcjmgmt```.
```
show schemas;
```
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.15.deploymysqlonebs.png?pc=60pt)

16. Create a new table name ```user``` inside database **fcjmgmt**.
```
use fcjmgmt;
CREATE TABLE `user` ( `id` INT NOT NULL AUTO_INCREMENT , `first_name` VARCHAR(45) NOT NULL , `last_name` VARCHAR(45) NOT NULL , `email` VARCHAR(45) NOT NULL , `phone` VARCHAR(45) NOT NULL , `comments` TEXT NOT NULL , `status` VARCHAR(10) NOT NULL DEFAULT 'active' , PRIMARY KEY (`id`)) ENGINE = InnoDB;
show tables;
```

![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.18.deploymysqlonebs.png?pc=60pt)


17. Enter ```exit``` to exit to your ephemeral pod. This ephemeral pod will be deleted after you exit to it.
![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.16.deploymysqlonebs.png?pc=60pt)


18. Go to [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes:v=3) to verify there is a new created volume with **Capacity** is **4Gi**.

![Deploy MySQL database on EBS Volume](../../images/3.eksdbwithebs/3.2.deploymysqlonebs/3.2.19.deploymysqlonebs.png?pc=60pt)

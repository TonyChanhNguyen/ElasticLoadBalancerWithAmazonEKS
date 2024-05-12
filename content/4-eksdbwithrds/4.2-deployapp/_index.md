---
title : "Deploy application"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

### Create Manifest files.
1. Create a working directory for this section.
```
cd ..
mkdir eks-with-rds
ls
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.1.deployapp.png?pc=60pt)

2. Copy **05-fcjmgmt-deployment.yaml** and **06-fcjmgmt-svc.yaml** on **kube-manifest** directory.
```
cp kube-manifest/05-fcjmgmt-deployment.yaml eks-with-rds
cp kube-manifest/06-fcjmgmt-svc.yaml eks-with-rds
ls eks-with-rds
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.2.deployapp.png?pc=60pt)

To help your EKS Cluster connect to created Amazon RDS Database, we will use **externalName** service to create the connection.

3. Create file named **07-RDS-externalName-svc.yaml** inside **eks-with-rds**.
```
touch eks-with-rds/07-RDS-externalName-svc.yaml
ls eks-with-rds
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.3.deployapp.png?pc=60pt)

4. Open file **07-RDS-externalName-svc.yaml**, paste the below code and save.
```
apiVersion: v1
kind: Service
metadata:
  name: mysql-svc
spec:
  type: ExternalName
  externalName: <REPLACE-WITH-YOUR-RDS-ENDPOINT>
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.4.deployapp.png?pc=60pt)

### Deploy resources
1. At Cloud9 terminal, execute the below command to deploy resources.
```
kubectl apply -f eks-with-rds
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.5.deployapp.png?pc=60pt)

2. List all created resources.
```
kubectl get deploy,svc,pod
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.6.deployapp.png?pc=60pt)

There is a created Deployment named **fcjmgmt-microservice**, a created Service named **fcjmgmt-restapp-service** with **Type** is **NodePort** and **PORT** is **31231**, a created Service named **mysql-svc** with **Type** is **ExternalName** and **EXTERNAL-IP** is **RDS-ENDPOINT**, and a created Pod with **STATUS** is **Running**.
3. Get the **Public IP Address** of Node which placing application Pod.
```
kubectl get node -o wide
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.7.deployapp.png?pc=90pt)

4. Access to application URL ```http://<REPLACE-WITH-NODE'S-EXTERNAL-IP>:31231```.
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.8.deployapp.png?pc=90pt)

Your application was deployed successfully.

5. Let add some users to your application.
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.9.deployapp.png?pc=90pt)

6. Now, let delete the current Deployment then re-create another one.
```
kubectl delete -f eks-with-rds/05-fcjmgmt-deployment.yaml
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.10.deployapp.png?pc=90pt)

7. List all running Deployment and Pod to make sure they are deleted totally.
```
kubectl get deploy,pod
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.11.deployapp.png?pc=90pt)
8. Let re-create Deployment and Pod.
```
kubectl apply -f eks-with-rds/05-fcjmgmt-deployment.yaml
kubectl get deploy,pod
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.12.deployapp.png?pc=90pt)

9. Reload your website to see the result.
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.13.deployapp.png?pc=90pt)

All of added user information are still kept while Deployment and Pod is rescheduled.

10. Morever, you can also perform **View**, **Edit** or **Delete** operations with your user information.

![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.14.deployapp.png?pc=90pt)

### Clean up
1. Delete all created resources.
```
kubectl delete -f eks-with-rds/
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.15.deployapp.png?pc=90pt)

2. List all resources to make sure they are deleted totally.
```
kubectl get deploy,svc,pod
```
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.16.deployapp.png?pc=90pt)

All of them are deleted totally.

3. Go to [RDS Database](https://ap-southeast-1.console.aws.amazon.com/rds/home?region=ap-southeast-1#databases:).
4. Select your database.
5. Click on **Action**.
6. Click on **Delete**.
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.17.deployapp.png?pc=90pt)

7. At **Delete fcj-management-db-instance instance** interface:
+ Uncheck **Create final snapshot**.
+ Uncheck **Retain automated backups**.
+ Check **I acknowledge that upon instance deletion, automated backups, including system snapshots and point-in-time recovery, will no longer be available.**
+ Input ```delete me``` to confirm.

8. Then, click on **Delete**.

![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.18.deployapp.png?pc=90pt)

![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.19.deployapp.png?pc=90pt)

9. After about 10 minutes, your database will be deleted.
![Deploy application](../../images/4.eksdbwithrds/4.2.deployapp/4.2.20.deployapp.png?pc=90pt)
---
title : "Deploy Application"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---
Now we will create manifest files to deploy application and its service.

### Deploy application
1. Create a file named **05-fcjmgmt-deployment.yaml**.
```
touch 05-fcjmgmt-deployment.yaml
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.1.deployappdeployment.png?pc=60pt)


2. Open **05-fcjmgmt-deployment.yaml** file, paste the below code. Replace ```<YOUR-REPOSITORY-PATH:TAG>``` with your repository name and tag. In my case is ```firstcloudjourneypcr/fcj-management:v1```.
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
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.2.deployappdeployment.png?pc=60pt)
3. Create a file named **06-fcjmgmt-svc.yaml**.
```
touch 06-fcjmgmt-svc.yaml
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.3.deployappdeployment.png?pc=60pt)

4. Open **06-fcjmgmt-svc.yaml** file, paste the below code and save it.
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
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.4.deployappdeployment.png?pc=60pt)

5. Let deploy your application.
```
kubectl apply -f .
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.5.deployappdeployment.png?pc=60pt)

6. List all created resources.
```
kubectl get sc,pv,pvc,deploy,svc,pod
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.6.deployappdeployment.png?pc=60pt)

There is a new created **Service** named **fcjmgmt-restapp-service** with **TYPE** is **NodePort** and **PORT** is **8080:31231/TCP**, a new created **Deployment** named **fcjmgmt-microservice** and **Pod** with **STATUS** is **Running**.

7. Let get the **Public IP Address** of your Node which placing Pod.
```
kubectl get node -o wide
```

![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.8.deployappdeployment.png?pc=60pt)
The **Public IP Address** is **EXTERNAL-IP**.

8. Let access to your application to see the result with URL ```http://<EXTERNAL-IP>:31231```.

![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.7.deployappdeployment.png?pc=60pt)

Opps, your application can not be accessed. This since the **Security Group** of your **Node** which placing Pod still not opened for port 31231.

9. Go to [Security Group](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#SecurityGroups:v=3;search=:eks-cluster-sg-fcj-db-cluster)/
10. Click on resulted security group.

![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.9.deployappdeployment.png?pc=60pt)

11. Click on **Edit inbound rules**.
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.10.deployappdeployment.png?pc=60pt)

12. Add a new rule with:
    + **Type** is **Custom TCP**.
    + **Port range** is **31231**.
    + **Source** is **Anywhere-IPv4**.
13. Click on **Save rules**.
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.11.deployappdeployment.png?pc=60pt)


14. Access again to application URL to see the result.
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.12.deployappdeployment.png?pc=60pt)

Your application had been deployed successfully.

### Test application
1. Click on **Add New User**.
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.13.deployappdeployment.png?pc=60pt)

2. Input all information and click on **Submit**.

![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.14.deployappdeployment.png?pc=60pt)

3. Repeat above step to add more users.

4. Then, click on **Home** to back to your homepage.

![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.15.deployappdeployment.png?pc=60pt)

5. Verify the result, there are some users had been add to your application.
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.16.deployappdeployment.png?pc=60pt)

Now we will delete application Pod and create new one verity see the data which you filled will be kept or not?

6. At Cloud9 terminal, delete application Pod.
```
kubectl delete -f 05-fcjmgmt-deployment.yaml
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.17.deployappdeployment.png?pc=60pt)

7. List all resources again to make sure application pod and deployment deleted.
```
kubectl get pod,deployment
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.18.deployappdeployment.png?pc=60pt)

They are deleted.

8. Let create them again.
```
kubectl apply -f 05-fcjmgmt-deployment.yaml
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.19.deployappdeployment.png?pc=60pt)

9. List all resources to make sure they are created.
```
kubectl get pod,deployment
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.20.deployappdeployment.png?pc=60pt)

10. Access to application URL again to verify the result.
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.21.deployappdeployment.png?pc=60pt)

All of data are kept while Pod and Deployment rescheduled.

### Clean up
1. At Cloud9 terminal, execute below command to delete all created resources.
```
kubectl delete -f .
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.22.deployappdeployment.png?pc=60pt)

2. List all resources again to make sure that they are deleted totally.
```
kubectl get sc,pv,pvc,deploy,svc,pod
```
![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.23.deployappdeployment.png?pc=60pt)
All of them are deleted totally.

3. Go to [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes:v=3) to verify the created EBS Volume deleted.

![Deploy Application Deployment](../../images/3.eksdbwithebs/3.3.deployappdeployment/3.3.24.deployappdeployment.png?pc=60pt)
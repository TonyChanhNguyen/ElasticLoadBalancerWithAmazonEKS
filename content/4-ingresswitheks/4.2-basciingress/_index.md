---
title : "Create Basic Ingress"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

In the previous section, you had installed the AWS Load Balancer Controller and created an Ingress Class. In this section, we will create a basic Ingress to integrate with our application.

### Create Manifest files
1. Create a new working directory for this section.
```
mkdir ingress/basic-ingress
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.1.basicingress.png?pc=90pt)


2. Create a manifest file named **01-App1-Deployment.yaml** to deploy your application.
```
touch ingress/basic-ingress/01-App1-Deployment.yaml
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.2.basicingress.png?pc=90pt)

3. Open file **01-App1-Deployment.yaml**, paste the below code. Then save it. Don't forget to replace with your container image URL or you can use ```firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v1``` for testing purpose.
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
            - containerPort: 8080
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.3.basicingress.png?pc=90pt)

4. Create a file named **02-NodePort-svc.yaml** to create NodePort for Application Pod.
```
touch ingress/basic-ingress/02-NodePort-svc.yaml
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.4.basicingress.png?pc=90pt)

5. Open file **02-NodePort-svc.yaml**, paste the below code. Then save it.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app1-nodeport-service
  labels:
    app: fcj-app1
spec:
  type: NodePort
  selector:
    app: fcj-app1
  ports:
    - port: 8080
      targetPort: 8080
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.5.basicingress.png?pc=90pt)

6. Create a file named **03-ALB-ingress.yaml** to create and integrate ALB Ingress Service to your application.
```
touch ingress/basic-ingress/03-ALB-ingress.yaml
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.6.basicingress.png?pc=90pt)

7. Open file **03-ALB-ingress.yaml**, paste the below code. Then save it.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fcj-app1-ingress
  labels:
    app: fcj-app1
  annotations:
    # Load Balancer Name
    alb.ingress.kubernetes.io/load-balancer-name: app1ingressrules
    # Ingress Core Settings
    alb.ingress.kubernetes.io/scheme: internet-facing
    # Health Check Settings
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
    alb.ingress.kubernetes.io/healthcheck-path: / 
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
    alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
    alb.ingress.kubernetes.io/success-codes: '200'
    alb.ingress.kubernetes.io/healthy-threshold-count: '2'
    alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'
spec:
  ingressClassName: my-aws-ingress-class # Ingress Class
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: fcj-app1-nodeport-service
            port:
              number: 8080
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.7.basicingress.png?pc=90pt)


### Deploy resources.
1. First, create a new Namespace named **fcj-basic-ingress-ns** to deploy resources for this section.
```
kubectl create ns fcj-basic-ingress-ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.8.basicingress.png?pc=90pt)

2. List your created Namespace.
```
kubectl get ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.9.basicingress.png?pc=90pt)

3. List all resources on Namespace **fcj-basic-ingress-ns** to make sure there are no resources created on it.
```
kubectl get ingress,deploy,svc,pod -n fcj-basic-ingress-ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.10.basicingress.png?pc=90pt)

4. Go to [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) to verify there is no ALB created.

![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.11.basicingress.png?pc=90pt)

5. Now, let create the resources on Namespace **fcj-basic-ingress-ns**.
```
kubectl apply -f ingress/basic-ingress -n fcj-basic-ingress-ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.12.basicingress.png?pc=90pt)

6. List your created resources.
```
kubectl get ingress,deploy,svc,pod -n fcj-basic-ingress-ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.13.basicingress.png?pc=90pt)

There are some created resources:
+ A created Ingress named **fcj-app1-ingress** with **Class** is **my-aws-ingress-class**, **ADDRESS** is **app1ingressrules-2077624863.ap-southeast-1.elb.amazonaws.com** and **PORTS** is **80**.
+ A created Deployment named **fcj-app1-deployment**.
+ A created Service named **fcj-app1-nodeport-service** with **TYPE** is **NodePort**.
+ A created Pod named **fcj-app1-deployment-xxxxxxxxxx-xxxxx**.

7. Go to [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) to see the result.
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.14.basicingress.png?pc=90pt)

The Application Load Balancer named **fcj-app1-ingress**, with **Type** is **application**, is created automatically when the Ingress created.

8. Wait to **State** of Application Load Balancer named **fcj-app1-ingress** change to **Active**. Click on it.

![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.15.basicingress.png?pc=90pt)

9. There is a **Listener**. Click on it.
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.16.basicingress.png?pc=90pt)

10. There is a **Target Group** routed by **Path Pattern /**. Click on it.
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.17.basicingress.png?pc=90pt)

11. The application was deploy on EC2 Instance of Node Group with **Port number** and **Health status** is **Healthy**.
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.18.basicingress.png?pc=90pt)


12. Let verify the result by accessing to your application URL ```http://<REPLACE-WITH-YOUR-INGRESS-ADDRESS>```.

![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.19.basicingress.png?pc=90pt)

#### Congratulations, you had deploy your application and integrate with Ingress (AWS Application Load Balancer) Service successfully.

### Clean up.
1. Execute the below command to delete resources on Namespace **fcj-basic-ingress-ns**.
```
kubectl delete -f ingress/basic-ingress -n fcj-basic-ingress-ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.20.basicingress.png?pc=90pt)

2. List all resources again to make sure they are deleted.
```
kubectl get ingress,deploy,svc,pod -n fcj-basic-ingress-ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.21.basicingress.png?pc=90pt)

3. Delete Namespace **fcj-basic-ingress-ns**.
```
kubectl delete ns fcj-basic-ingress-ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.22.basicingress.png?pc=90pt)

4. Make sure that it is deleted.
```
kubectl get ns fcj-basic-ingress-ns
```
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.23.basicingress.png?pc=90pt)

5. Go to [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) to verify the Application Load Balancer deleted automatically when Ingress deleted.
![Create Basic Ingress](../../images/4.ingresswitheks/4.2.basicingress/4.2.24.basicingress.png?pc=90pt)
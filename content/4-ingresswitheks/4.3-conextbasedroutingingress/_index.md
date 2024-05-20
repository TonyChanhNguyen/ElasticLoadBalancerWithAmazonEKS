---
title : "Context Path Based Routing Ingress on EC2 Managed NodeGroup"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---

We are going to deploy all these 2 apps in kubernetes with context path based routing enabled in Ingress Controller:
+ /app1/* - should go to application App1.
+ /app2/* - should go to application App2.

### Create Manifest files.
1. Create a new working directory for this section.
```
mkdir ingress/context-based-routing-ingress
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.1.contextbasedroutingingress.png?pc=90pt)


2. Create a filed name **01-App1-Deployment.yaml** inside **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/01-App1-Deployment.yaml
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.2.contextbasedroutingingress.png?pc=90pt)

3. Open file **01-App1-Deployment.yaml**, paste the below code. Then save it. We will use container image URL ```stacksimplify/kube-nginxapp1:1.0.0``` for testing purpose.
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
          image: stacksimplify/kube-nginxapp1:1.0.0
          ports:
            - containerPort: 80
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.3.contextbasedroutingingress.png?pc=90pt)

4. Create a filed name **02-App1-NodePort-svc.yaml** inside **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/02-App1-NodePort-svc.yaml
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.4.contextbasedroutingingress.png?pc=90pt)

5. Open file **02-App1-NodePort-svc.yaml**, paste the below code. Then save it.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app1-nodeport-service
  labels:
    app: fcj-app1
  annotations:  
    alb.ingress.kubernetes.io/healthcheck-path: /app1/index.html
spec:
  type: NodePort
  selector:
    app: fcj-app1
  ports:
    - port: 80
      targetPort: 80
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.5.contextbasedroutingingress.png?pc=90pt)

6. Create a filed name **03-App2-Deployment.yaml** inside **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/03-App1-Deployment.yaml
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.6.contextbasedroutingingress.png?pc=90pt)

7. Open file **03-App2-Deployment.yaml**, paste the below code. Then save it. We will use container image URL ```stacksimplify/kube-nginxapp2:1.0.0``` for testing purpose.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fcj-app2-deployment
  labels:
    app: fcj-app2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fcj-app2
  template:
    metadata:
      labels:
        app: fcj-app2
    spec:
      containers:
        - name: fcj-app2
          image: stacksimplify/kube-nginxapp2:1.0.0
          ports:
            - containerPort: 80
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.7.contextbasedroutingingress.png?pc=90pt)


8. Create a filed name **04-App2-NodePort-svc.yaml** inside **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/04-App2-NodePort-svc.yaml
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.8.contextbasedroutingingress.png?pc=90pt)

9. Open file **04-App2-NodePort-svc.yaml**, paste the below code. Then save it.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app2-nodeport-service
  labels:
    app: fcj-app2
  annotations:  
    alb.ingress.kubernetes.io/healthcheck-path: /app2/index.html
spec:
  type: NodePort
  selector:
    app: fcj-app2
  ports:
    - port: 80
      targetPort: 80
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.9.contextbasedroutingingress.png?pc=90pt)

10. Create a filed name **05-ALB-ingress.yaml** inside **ingress/context-based-routing-ingress**.
```
touch ingress/context-based-routing-ingress/05-ALB-ingress.yaml
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.10.contextbasedroutingingress.png?pc=90pt)

11. Open file **05-ALB-ingress.yaml**, paste the below code. Then save it.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fcj-context-based-routing-ingress
  annotations:
    # Load Balancer Name
    alb.ingress.kubernetes.io/load-balancer-name: fcj-context-based-routing
    # Ingress Core Settings
    alb.ingress.kubernetes.io/scheme: internet-facing
    # Health Check Settings
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
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
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: fcj-app1-nodeport-service
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: fcj-app2-nodeport-service
            port:
              number: 80
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.11.contextbasedroutingingress.png?pc=90pt)

### Deploy resources.
1. Create new Namespace to deploy resources on.
```
kubectl create ns fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.12.contextbasedroutingingress.png?pc=90pt)

2. List created Namespace.
```
kubectl get ns fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.13.contextbasedroutingingress.png?pc=90pt)

3. Deploy resources on created Namespace.
```
kubectl apply -f ingress/context-based-routing-ingress -n fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.14.contextbasedroutingingress.png?pc=90pt)


4. List created resources.
```
kubectl get ingress,svc,deploy,pod -n fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.15.contextbasedroutingingress.png?pc=90pt)

There are some created resources:
+ A created Ingress named **fcj-context-based-routing-ingress** with **ADDRESS** is **fcj-context-based-routing-1220596484.ap-southeast-1.elb.amazonaws.com**.
+ Two created Service named **fcj-app1-nodeport-service** and **fcj-app2-nodeport-service** for Application App1 and App2.
+ Two created Deploymet named **fcj-app1-deployment** and **fcj-app2-deployment**, and two created Pod for each Application.

5. Go to [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) to verify that there is a Application Load Balancer created automatically.
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.16.contextbasedroutingingress.png?pc=90pt)


6. Verify **Listener Rule** of Application Load Balancer. There are two created rules: One for App1 and one for App2.
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.17.contextbasedroutingingress.png?pc=90pt)

7. Access to your **Ingress Address** to verify the result:
+ Access with ```/app1``` to go to Application App1.
+ Access with ```/app2``` to go to Application App2.
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.18.contextbasedroutingingress.png?pc=90pt)
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.19.contextbasedroutingingress.png?pc=90pt)

### Clean up
1. Execute the below command to delete resources on Namespace **fcj-context-based-routing-ns**.
```
kubectl delete -f ingress/context-based-routing-ingress -n fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.20.contextbasedroutingingress.png?pc=90pt)

2. List all resources again to make sure they are deleted.
```
kubectl get ingress,svc,deploy,pod -n fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.21.contextbasedroutingingress.png?pc=90pt)

3. Delete Namespace **fcj-context-based-routing-ns**.
```
kubectl delete ns fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.22.contextbasedroutingingress.png?pc=90pt)

4. Make sure that it is deleted.
```
kubectl get ns fcj-context-based-routing-ns
```
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.23.contextbasedroutingingress.png?pc=90pt)

5. Go to [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) to verify the Application Load Balancer deleted automatically when Ingress deleted.
![Context Based Routing Ingress](../../images/4.ingresswitheks/4.3.contextbasedroutingingress/4.3.24.contextbasedroutingingress.png?pc=90pt)
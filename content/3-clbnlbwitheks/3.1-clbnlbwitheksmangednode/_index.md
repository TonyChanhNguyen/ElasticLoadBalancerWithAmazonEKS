---
title : "Classic Load Balancer service with Amazon EKS Cluster EC2 Managed NodeGroup"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---

### Create Manifest files
1. Create a directory for **CLB** section.
```
cd ..
mkdir clb
cd clb
```

![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.1.clbnlbwitheksmangednode.png?pc=90pt)

2. Create a file named **app-deployment.yaml** inside **clb**.
```
touch app-deployment.yaml
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.2.clbnlbwitheksmangednode.png?pc=90pt)

3. Open file **app-deployment.yaml**, paste the below code. Then save it. Do not forget to replace **`<REPLACE-WITH-YOUR-CONTAINER-IMAGE-URL>`** with your container image URL on DockerHub.
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
            - containerPort: 80
```
In my case, container image URL on DockerHub is ```firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v1```. You can replace with it for testing purpose.

![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.3.clbnlbwitheksmangednode.png?pc=90pt)

4. Create a file named **ClassicLoadBalancer.yaml** inside **clb**.
```
touch ClassicLoadBalancer.yaml
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.4.clbnlbwitheksmangednode.png?pc=90pt)


5. Open file **ClassicLoadBalancer.yaml**, paste the below code. Then save it.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app1-clb-svc
  labels: 
    app: fcj-app1
spec:
  type: LoadBalancer # Default - CLB
  selector:
    app: fcj-app1
  ports: 
    - port: 8080
      targetPort: 8080
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.5.clbnlbwitheksmangednode.png?pc=90pt)


### Deploy resources
1. Let create a namespace to deploy resources.
```
kubectl create ns fcj-app1
kubectl get ns
```

![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.6.clbnlbwitheksmangednode.png?pc=90pt)

2. Execute the below command to deploy resources to **fcj-app1** namespace.
```
kubectl apply -n fcj-app1 -f .
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.7.clbnlbwitheksmangednode.png?pc=90pt)

3. List all created resources on **fcj-app1** namespace.
```
kubectl get deploy,pod,svc -n fcj-app1
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.8.clbnlbwitheksmangednode.png?pc=90pt)

There are some created resources on **fcj-app1** namespace:
+ A Deployment named **fcj-app1-deployment**.
+ A Pod named **fcj-app1-deployment-7d9d4f7c44-ddvj8** with **STATUS** is **Running**.
+ A Service named **fcj-app1-clb-svc** with **TYPE** is **LoadBalancer** and **EXTERNAL-IP** is **a89ef47f06ea949c2b6e6e91bafc6545-898842507.ap-southeast-1.elb.amazonaws.com**.

4. Let access to [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3). There is a created Load Balancer with **Type** is **classic** and **DNS name** match with **EXTERNAL-IP** of **fcj-app1-clb-svc** Service.
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.9.clbnlbwitheksmangednode.png?pc=90pt)

5. Let access to URL ```http://<YOUR-EXTERNAL-IP>:8080``` to see the result.
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.10.clbnlbwitheksmangednode.png?pc=90pt)

Your application was deployed with Classic Load Balancer successfully.

### Clean up.
1. Execute the below command to delete all created resources.
```
kubectl delete -n fcj-app1 -f .
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.11.clbnlbwitheksmangednode.png?pc=90pt)

2. List all resources to make sure they are deleted.
```
kubectl get deploy,pod,svc -n fcj-app1
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.12.clbnlbwitheksmangednode.png?pc=90pt)

3. Delete created namespace.
```
kubectl delete ns fcj-app1
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.13.clbnlbwitheksmangednode.png?pc=90pt)

4. List all namespace to make sure it is deleted.
```
kubectl get ns
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.14.clbnlbwitheksmangednode.png?pc=90pt)

5. Access to [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) again to verify the created Classic Load Balancer was deleted automatically when **fcj-app1-clb-svc** Service deleted.
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.1.clbnlbwitheksmangednode/3.1.15.clbnlbwitheksmangednode.png?pc=90pt)
---
title : "Target Type IP"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---
In this section, we will deploy application on Fargate Node with Ingress and ExternalDNS Service.

### Create Manifest file.
1. Create new working directory for this section.
```
mkdir ingress/externaldns/target-type-ip
ls ingress/externaldns
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.1.targettypeip.png?pc=90pt)

2. Create folder **farget-profile** to store fargate profile definition file.
```
mkdir ingress/externaldns/target-type-ip/farget-profile
ls ingress/externaldns/target-type-ip
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.2.targettypeip.png?pc=90pt)

3. Create **fargate-profile.yaml** inside **ingress/externaldns/target-type-ip/farget-profile**.
```
touch ingress/externaldns/target-type-ip/farget-profile/fargate-profile.yaml
ls ingress/externaldns/target-type-ip/farget-profile/
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.3.targettypeip.png?pc=90pt)

4. Open file **fargate-profile.yaml** and paste the below code. Then save it.
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
    name: fcj-elb-cluster
    region: ap-southeast-1
fargateProfiles:
    - name: fcj-fp
      selectors:
        - namespace: fcj-external-dns-ingress-ns
          labels:
            runon: fargate
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.4.targettypeip.png?pc=90pt)

5. Create new folder to store manifest files to deploy application.
```
mkdir ingress/externaldns/target-type-ip/application
ls ingress/externaldns/target-type-ip
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.5.targettypeip.png?pc=90pt)

6. Create a file named **01-App1-Deployment.yaml** inside **ingress/externaldns/target-type-ip/application**.
```
touch ingress/externaldns/target-type-ip/application/01-App1-Deployment.yaml
ls ingress/externaldns/target-type-ip/application
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.6.targettypeip.png?pc=90pt)

7. Open file **01-App1-Deployment.yaml** and paste the below code. Then save it.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fcj-app1-deployment
  labels:
    app: fcj-app1
    runon: fargate
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fcj-app1
  template:
    metadata:
      labels:
        app: fcj-app1
        runon: fargate 
    spec:
      containers:
        - name: fcj-app1
          image: stacksimplify/kube-nginxapp1:1.0.0
          ports:
            - containerPort: 80
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.7.targettypeip.png?pc=90pt)

8. Create a file named **02-App1-ClusterIP.yaml** inside **ingress/externaldns/target-type-ip/application**.
```
touch ingress/externaldns/target-type-ip/application/02-App1-ClusterIP.yaml
ls ingress/externaldns/target-type-ip/application
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.8.targettypeip.png?pc=90pt)

9. Open file **02-App1-ClusterIP.yaml** and paste the below code. Then save it.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app1-clusterip-service
  labels:
    app: fcj-app1
    runon: fargate
  annotations:
    alb.ingress.kubernetes.io/healthcheck-path: /app1/index.html
spec:
  type: ClusterIP
  selector:
    app: fcj-app1
  ports:
    - port: 80
      targetPort: 80
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.9.targettypeip.png?pc=90pt)

10. Similarly, create a file named **03-App2-Deployment.yaml** and paste the below code. Then save it.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fcj-app2-deployment
  labels:
    app: fcj-app2
    runon: fargate
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fcj-app2
  template:
    metadata:
      labels:
        app: fcj-app2
        runon: fargate
    spec:
      containers:
        - name: fcj-app2
          image: stacksimplify/kube-nginxapp2:1.0.0
          ports:
            - containerPort: 80
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.10.targettypeip.png?pc=90pt)

11. Similarly, create a file named **04-App2-ClusterIP.yaml** and paste the below code. Then save it.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-app2-clusterip-service
  labels:
    app: fcj-app2
    runon: fargate
  annotations:
    alb.ingress.kubernetes.io/healthcheck-path: /app2/index.html
spec:
  type: ClusterIP
  selector:
    app: fcj-app2
  ports:
    - port: 80
      targetPort: 80
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.11.targettypeip.png?pc=90pt)

12. Similarly, create a file named **05-ALB-ingress.yaml**, paste the below code and replace ```<REPLACE-WITH-YOUR-HOST-HEADER-APP1>``` and ```<REPLACE-WITH-YOUR-HOST-HEADER-APP2>``` with your Host Header . Then save it.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fcj-target-type-ip-ingress
  labels:
    runon: fargate
  annotations:
    # Load Balancer Name
    alb.ingress.kubernetes.io/load-balancer-name: fcj-target-type-ip
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
    ## SSL Settings
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS":443}, {"HTTP":80}]'
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:ap-southeast-1:170074558790:certificate/3798d174-1dc8-4380-974b-c219a4317730
    # SSL Redirect Setting
    alb.ingress.kubernetes.io/ssl-redirect: '443'   
    # For Fargate
    alb.ingress.kubernetes.io/target-type: ip 
spec:
  ingressClassName: my-aws-ingress-class # Ingress Class
  rules:
  - host: <REPLACE-WITH-YOUR-HOST-HEADER-APP1>
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: fcj-app1-clusterip-service
            port:
              number: 80
  - host: <REPLACE-WITH-YOUR-HOST-HEADER-APP2>
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: fcj-app2-clusterip-service
            port:
              number: 80
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.12.targettypeip.png?pc=90pt)


### Deploy resources.
#### Create Fargate Profile
1. Create Fargate Profile on your EKS Cluster.
```
eksctl create fargateprofile -f ingress/externaldns/target-type-ip/farget-profile/fargate-profile.yaml
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.13.targettypeip.png?pc=90pt)

2. List Fargate Profile on EKS Cluster again.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.14.targettypeip.png?pc=90pt)

#### Deploy resources.
1. Deploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/target-type-ip/application -n fcj-external-dns-ingress-ns
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.15.targettypeip.png?pc=90pt)

2. List created resources.
```
kubectl get all -n fcj-external-dns-ingress-ns
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.16.targettypeip.png?pc=90pt)

3. List all Nodes in your Cluster.
```
kubectl get node -o wide
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.17.targettypeip.png?pc=90pt)

There are two create Fargate Instances for application App1 and App2.

### Verify the result.
1. Access to ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.18.targettypeip.png?pc=90pt)

2. Access to ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2```.
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.19.targettypeip.png?pc=90pt)

### Clean up
1. Delete the created resources.
```
kubectl delete -f ingress/externaldns/target-type-ip/application -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.20.targettypeip.png?pc=90pt)

We will retain resources which related to **external-dns** service and created **SSL certificate** on **AWS Certificate Manager** for next sections.

2. List current Node on your Cluster to verify two created Fargate Nodes deleted automatically.
```
kubectl get node -o wide
```
![Target Type IP](../../images/5.externaldns/5.5.targettypeip/5.5.21.targettypeip.png?pc=90pt)
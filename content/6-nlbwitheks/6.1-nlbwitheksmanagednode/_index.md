---
title : "Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 6.1 </b> "
---

## Deploy Basic Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node
#### Create Manifest file.
1. Create new directory for this section.
```
mkdir nlb
ls
```
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.1.nlbwitheksmanagednode.png?pc=90pt)

2. Create new working directory.
```
mkdir nlb/ManagedNodeGroup
ls nlb
```
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.2.nlbwitheksmanagednode.png?pc=90pt)

3. Copy manifest file named **app-deployment.yaml** from **clb/ManagedNodeGroup/** to **nlb/ManagedNodeGroup**.
```
cp clb/ManagedNodeGroup/app-deployment.yaml nlb/ManagedNodeGroup
ls nlb/ManagedNodeGroup
```
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.3.nlbwitheksmanagednode.png?pc=90pt)

4. Create a new file named **NetworkLoadBalancer.yaml** inside **nlb/ManagedNodeGroup**.
```
touch nlb/ManagedNodeGroup/NetworkLoadBalancer.yaml
ls nlb/ManagedNodeGroup
```
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.4.nlbwitheksmanagednode.png?pc=90pt)

5. Open file **NetworkLoadBalancer.yaml**, paste the code below and save it.
```
apiVersion: v1
kind: Service
metadata:
  name: fcj-basic-nlb-service
  annotations:
    # Traffic Routing
    service.beta.kubernetes.io/aws-load-balancer-name: fcj-basic-nlb
    service.beta.kubernetes.io/aws-load-balancer-type: external
    
    # Health Check Settings
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-protocol: http
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-port: traffic-port
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-path: /
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-healthy-threshold: "3"
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-unhealthy-threshold: "3"
    service.beta.kubernetes.io/aws-load-balancer-healthcheck-interval: "10" 

    # Access Control
    service.beta.kubernetes.io/load-balancer-source-ranges: 0.0.0.0/0 
    service.beta.kubernetes.io/aws-load-balancer-scheme: "internet-facing"

spec:
  type: LoadBalancer
  selector:
    app: fcj-app1
  ports:
    - port: 80
      targetPort: 80
```
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.5.nlbwitheksmanagednode.png?pc=90pt)


#### Deploy resources.
1. Execute the below command to deploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```

![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.6.nlbwitheksmanagednode.png?pc=90pt)

There are some created resources:
+ A created Deployment named **fcj-app1-deployment** with **replicas** is **1**.
+ A created ReplicaSet named **fcj-app1-deployment-xxxxxxxxxx**  with **replicas** is **1**.
+ A created Pod named **fcj-app1-deployment-xxxxxxxxxx-xxxxx** with **STATUS** is **Running**.
+ A created Service named **fcj-basic-nlb-service** with **Type** is **LoadBalancer** and **EXTERNAL-IP** is **fcj-basic-nlb-b85e5b7a2c14c0d3.elb.ap-southeast-1.amazonaws.com**.

2. Let access to [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3). There is a created Load Balancer with **Type** is **network** and **DNS name** match with **EXTERNAL-IP** of **fcj-basic-nlb-service** Service.
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.7.nlbwitheksmanagednode.png?pc=90pt)

3. Click on your Load Balancer to verify the **Listeners**. When we access to application via **TCP:8080**, it will be forwarded to created **TargetGroup**.
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.8.nlbwitheksmanagednode.png?pc=90pt)

4. Click on to created **TargetGroup** to verify the **Registered targets**.
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.9.nlbwitheksmanagednode.png?pc=90pt)

The **Registered targets** is the EC2 Managed Node on your Cluster with **Health status** is **Healthy**.

#### Verify the result.
1. Access to your application URL ```http://<YOUR-NETWORK-LOAD-BALANCER-URL>:8080```.
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.10.nlbwitheksmanagednode.png?pc=90pt)


## (Optional) Integrate with ExternalDNS service.
#### Modify Manifest file.
1. Open file **NetworkLoadBalancer.yaml**, add the below annotations to integrate with ExternalDNS service. Then save it.
```
# External DNS - For creating a Record Set in Route53
external-dns.alpha.kubernetes.io/hostname: fcjnlb.<YOUR-DOMAIN-NAME>
```
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.11.nlbwitheksmanagednode.png?pc=90pt)

#### Deploy resources.
1. Execute the below command to redeploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.12.nlbwitheksmanagednode.png?pc=90pt)
The Service named **fcj-basic-nlb-service** will be configured.

#### Verify result.
1. Access to ```http://fcjnlb.<YOUR-DOMAIN-NAME>:8080``` to verify the result.

![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.13.nlbwitheksmanagednode.png?pc=90pt)

#### Clean up.
1. Delete the created resources.
```
kubectl delete -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.14.nlbwitheksmanagednode.png?pc=90pt)

We will retain resources which related to **external-dns** service and created **SSL certificate** on **AWS Certificate Manager** for next sections.

2. Access to  [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) to verify that the created Network Load Balancer deleted automatically when Service **fcj-basic-nlb-service** was deleted.
![Network Load Balancer service with Amazon EKS Cluster EC2 Managed Node](../../images/6.nlbwitheks/6.1.nlbwitheksmanagednode/6.1.15.nlbwitheksmanagednode.png?pc=90pt)
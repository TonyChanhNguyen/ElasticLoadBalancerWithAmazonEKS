---
title : "Network Load Balancer service with Amazon EKS Cluster Fargate Node"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 6.2 </b> "
---

## Deploy Basic Network Load Balancer service with Amazon EKS Cluster Fargate Node
#### Modify Manifest file.
Because the Fargate Profile was created in the previous section, so we will re-use it. Let add **labels** is **runon: Fargate** to our application manifest files.
1. Open file named **app-deployment.yaml** inside **nlb/ManagedNodeGroup**. Add **labels** is **runon: Fargate** at **metadata.labels** and **spec.template.metadata.labels** parameter. Then save it.
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.1.nlbwitheksfargatenode.png?pc=90pt)

2. Open file named **NetworkLoadBalancer.yaml** inside **nlb/ManagedNodeGroup**. Add **labels** is **runon: Fargate** at **metadata** parameter. Additionally, toggle to comment the definition of **External DNS** at line **26**. Then save it. 
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.2.nlbwitheksfargatenode.png?pc=90pt)

#### Deploy resources.
1. Deploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.3.nlbwitheksfargatenode.png?pc=90pt)

2. List created resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl get all -n fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.4.nlbwitheksfargatenode.png?pc=90pt)
3. List all Nodes in your Cluster.
```
kubectl get node -o wide
```
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.5.nlbwitheksfargatenode.png?pc=90pt)

There are a created Fargate Instance for application.

4. Let describe your Pod to verify it is running on Fargate Instance. Replace with Pod ID and verify that the **IP** of Pod match with **INTERNAL-IP** of Fargate Instance and **Node** of Pod is the name of Fargate Instance.
```
kubectl describe pod <POD-ID> -n fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.7.nlbwitheksfargatenode.png?pc=90pt)


5. Access to  [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) to verify that the created Network Load Balancer.
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.6.nlbwitheksfargatenode.png?pc=90pt)


#### Verify the result.
1. Access to your application URL ```http://<YOUR-NETWORK-LOAD-BALANCER-URL>:8080```.
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.8.nlbwitheksfargatenode.png?pc=90pt)

The application works!

## (Optional) Integrate with ExternalDNS service.
#### Modify Manifest file.
1. Open file **NetworkLoadBalancer.yaml**, uncomment ExternalDNS definition at line 26 and replace with ```fcjnlbfargate.<YOUR-DOMAIN-NAME>```. Then save it.

![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.9.nlbwitheksfargatenode.png?pc=90pt)

#### Redeploy resources.
1. Execute the below command to redeploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.10.nlbwitheksfargatenode.png?pc=90pt)
The Service named **fcj-basic-nlb-service** will be configured.

#### Verify result.
1. Access to ```http://fcjnlbfargate.<YOUR-DOMAIN-NAME>:8080``` to verify the result.
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.11.nlbwitheksfargatenode.png?pc=90pt)


## Clean up
#### Delete created resources.
1. Delete your created resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl delete -f nlb/ManagedNodeGroup -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.12.nlbwitheksfargatenode.png?pc=90pt)

#### Delete ExternalDNS Service
1. Delete ExternalDNS Service on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl delete -f ingress/externaldns/externaldns-deployment -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.13.nlbwitheksfargatenode.png?pc=90pt)

### Delete created Namespace.
1. Delete created Namespace **fcj-external-dns-ingress-ns**.
```
kubectl delete ns fcj-external-dns-ingress-ns
kubectl get ns fcj-external-dns-ingress-ns
```
![Network Load Balancer service with Amazon EKS Cluster Fargate Node](../../images/6.nlbwitheks/6.2.nlbwitheksfargatenode/6.2.14.nlbwitheksfargatenode.png?pc=90pt)

#### Congratulations, you had deploy your application to Amazon EKS Cluster on Fargate Node with Ingress and ExternalDNS service successfully.
---
title : "Context Path Based Routing Ingress on Fargate Profile"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4.4 </b> "
---

In previous section, we deployed all these 2 apps in kubernetes with context path based routing with Ingress Controller on EC2 Managed Node.
In this section, we are going to deploy them on Fargate Profile.

### Create Manifest file
1. Create new working directory for this section.
```
mkdir ingress/context-based-routing-ingress-on-fargate
```

![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.1.contextbasedroutingingressonfargate.png?pc=90pt)

2. Create Fargate Profile manifest file.
```
mkdir ingress/context-based-routing-ingress-on-fargate/fargate-profile
ls ingress/context-based-routing-ingress-on-fargate
touch ingress/context-based-routing-ingress-on-fargate/fargate-profile/fargate-profile.yaml
ls ingress/context-based-routing-ingress-on-fargate/fargate-profile
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.2.contextbasedroutingingressonfargate.png?pc=90pt)

3. Open file **fargate-profile.yaml**, paste the code below. Then save it.
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
    name: fcj-elb-cluster
    region: ap-southeast-1
spec:
    - name: fcj-fp
      selectors:
        - namespace: fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.3.contextbasedroutingingressonfargate.png?pc=90pt)

4. Create a new folder to store application manifest files.
```
mkdir ingress/context-based-routing-ingress-on-fargate/application
ls ingress/context-based-routing-ingress-on-fargate
```

![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.4.contextbasedroutingingressonfargate.png?pc=90pt)

5. Copy all application manifest files on previous section inside **ingress/context-based-routing-ingress** to **ingress/context-based-routing-ingress-on-fargate/application**.
```
cp ingress/context-based-routing-ingress/* ingress/context-based-routing-ingress-on-fargate/application
ls ingress/context-based-routing-ingress-on-fargate/application
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.5.contextbasedroutingingressonfargate.png?pc=90pt)


### Deploy resource
#### Create namespace to deploy resources on.
1. Create the namespace named **fcj-context-path-based-routing-fargate-ns**, matched with value of parameter **spec.selectors.namespace** on **ingress/context-based-routing-ingress-on-fargate/fargate-profile/fargate-profile.yaml**.
```
kubectl create ns fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.6.contextbasedroutingingressonfargate.png?pc=90pt)

2. List your created Namespace.
```
kubectl get ns fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.7.contextbasedroutingingressonfargate.png?pc=90pt)

#### Create Fargate Profile.
1. Get the current Fargate Profile on EKS Cluster.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.8.contextbasedroutingingressonfargate.png?pc=90pt)

2. Create Fargate Profile on your EKS Cluster.
```
eksctl create fargateprofile -f ingress/context-based-routing-ingress-on-fargate/fargate-profile/fargate-profile.yaml
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.9.contextbasedroutingingressonfargate.png?pc=90pt)

3.List Fargate Profile on EKS Cluster again.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1
```

![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.10.contextbasedroutingingressonfargate.png?pc=90pt)

#### Deploy resources.
1. Execute the below command to deploy resources on Namespace **fcj-context-path-based-routing-fargate-ns**.
```
kubectl apply -f ingress/context-based-routing-ingress-on-fargate/application -n fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.11.contextbasedroutingingressonfargate.png?pc=90pt)

2. List you created resources.
```
kubectl get ingress,svc,deploy,pod -n fcj-context-path-based-routing-fargate-ns
```

![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.12.contextbasedroutingingressonfargate.png?pc=90pt)

There are some created resources:
+ A created Ingress named **fcj-context-based-routing-ingress** with **ADDRESS** is **fcj-context-based-routing-427025104.ap-southeast-1.elb.amazonaws.com**.
+ Two created Service named **fcj-app1-nodeport-service** and **fcj-app2-nodeport-service** for Application App1 and App2.
+ Two created Deploymet named **fcj-app1-deployment** and **fcj-app2-deployment**, and two created Pod for each Application.

3. Let list your current Node to verify that Fargate Node created.
```
kubectl get node -o wide
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.13.contextbasedroutingingressonfargate.png?pc=90pt)

There are two created Fargate Node.

#### Verify the result.
1. Access to ```http://<REPLACE-WITH-INGRESS-ADDRESS>/app1``` to verify the result.

![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.14.contextbasedroutingingressonfargate.png?pc=90pt)


2. Access to ```http://<REPLACE-WITH-INGRESS-ADDRESS>/app2``` to verify the result.

![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.15.contextbasedroutingingressonfargate.png?pc=90pt)


#### Congratulations, you had deploy your application with Ingress Service to Fargate Node successfully.

### Clean up.
1. Execute the below command to delete created resources.
```
kubectl delete -f ingress/context-based-routing-ingress-on-fargate/application -n fcj-context-path-based-routing-fargate-ns
kubectl get ingress,svc,deploy,pod -n fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.16.contextbasedroutingingressonfargate.png?pc=90pt)

All of them are deleted.

2. Delete the Fargate Profile.
```
eksctl delete fargateprofile --name fcj-fp --cluster fcj-elb-cluster --region ap-southeast-1 --wait
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.17.contextbasedroutingingressonfargate.png?pc=90pt)

3. Delete the created Namespace.
```
kubectl delete ns fcj-context-path-based-routing-fargate-ns
kubectl get ns fcj-context-path-based-routing-fargate-ns
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.18.contextbasedroutingingressonfargate.png?pc=90pt)


4. Verify that Fargate Nodes are deleted.
```
kubectl get node -o wide
```
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.19.contextbasedroutingingressonfargate.png?pc=90pt)

There is only an EC2 Managed Node.

5. Go to [Load Balancers](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) to verify the Application Load Balancer deleted automatically when Ingress deleted.
![Context Path Based Routing Ingress on Fargate Profile](../../images/4.ingresswitheks/4.4.contextbasedroutingingressonfargate/4.4.20.contextbasedroutingingressonfargate.png?pc=90pt)

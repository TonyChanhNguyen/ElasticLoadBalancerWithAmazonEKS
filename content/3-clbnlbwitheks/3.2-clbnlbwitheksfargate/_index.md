---
title : "Classic Load Balancer service with Amazon EKS Cluster Fargate Profile"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2</b> "
---

1. Move all resources of previous section to a new directory named **ManagedNodeGroup**.
```
mkdir ManagedNodeGroup
mv app-deployment.yaml ManagedNodeGroup/
mv ClassicLoadBalancer.yaml ManagedNodeGroup/
ls ManagedNodeGroup
```

![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.1.clbnlbwitheksfargate.png?pc=90pt)

2. Create new directory named **Fargate** for this section.
```
mkdir Fargate
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.2.clbnlbwitheksfargate.png?pc=90pt)


### Create Fargate Profile
1. Move to **Fargate** directory and create directory named **FargateProfile**.
```
cd Fargate
mkdir FargateProfile
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.3.clbnlbwitheksfargate.png?pc=90pt)

2. Create a file name **fargate-profiles.yml** inside **FargateProfile**.
```
touch FargateProfile/fargate-profiles.yml
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.4.clbnlbwitheksfargate.png?pc=90pt)

3. Open file **fargate-profiles.yml**, paste the below code. Then save it.
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
    name: fcj-elb-cluster   #Cluster Name
    region: ap-southeast-1  # Region ID
fargateProfiles:
    - name: fp-app1
      selectors:
        - namespace: ns-fcj-fp
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.5.clbnlbwitheksfargate.png?pc=90pt)

4. To create Fargate Profile, you need to create namespace which match with provided **fargateProfiles.selectors.namespace** parameter value on **fargate-profiles.yml** manifest file.
```
kubectl create ns ns-fcj-fp
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.6.clbnlbwitheksfargate.png?pc=90pt)

5. List your created namespace.
```
kubectl get ns
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.7.clbnlbwitheksfargate.png?pc=90pt)

6. Create Fargate Profile.
```
eksctl create fargateprofile -f FargateProfile/fargate-profiles.yml
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.8.clbnlbwitheksfargate.png?pc=90pt)

7. List created Fargate Profile.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1 -o yaml
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.9.clbnlbwitheksfargate.png?pc=90pt)


### Create Manifest file
We will re-use the Manifest files in previous section.
1. Create a folder name **kube-manifest**.
```
mkdir kube-manifest
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.10.clbnlbwitheksfargate.png?pc=90pt)

2. Copy all manifest files of **ManagedNodeGroup** to **Fargate/kube-manifest**.
```
cd ..
cp -r ManagedNodeGroup/* Fargate/kube-manifest
cd Fargate
ls kube-manifest
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.11.clbnlbwitheksfargate.png?pc=90pt)

### Deploy resources
1. Execute the below command to deploy resources on namespace **ns-fcj-fp** which defined on Fargate Profile.
```
kubectl apply -n ns-fcj-fp -f kube-manifest
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.12.clbnlbwitheksfargate.png?pc=90pt)

2. List all created resources. It will take about 3 minutes for all resources created successfully.
```
kubectl get -n ns-fcj-fp deploy,pod,svc
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.13.clbnlbwitheksfargate.png?pc=90pt)

There are some created resources on **fcj-app1** namespace:
+ A Deployment named **fcj-app1-deployment**.
+ A Pod named **fcj-app1-deployment-7d9d4f7c44-lbqc6** with **STATUS** is **Running**.
+ A Service named **fcj-app1-clb-svc** with **TYPE** is **LoadBalancer** and **EXTERNAL-IP** is **a40e3cb6afdf84f60b8b8064284ad7cf-785887277.ap-southeast-1.elb.amazonaws.com**.

3. Let access to [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3). There is a created Load Balancer with **Type** is **classic** and **DNS name** match with **EXTERNAL-IP** of **fcj-app1-clb-svc** Service.
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.14.clbnlbwitheksfargate.png?pc=90pt)

4. Let access to URL ```http://<YOUR-EXTERNAL-IP>:8080``` to see the result.
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.15.clbnlbwitheksfargate.png?pc=90pt)

Your application was deployed with Classic Load Balancer successfully.

5. Let list all running Nodes in your Cluster.
```
kubectl get nodes
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.16.clbnlbwitheksfargate.png?pc=90pt)

We has 2 types of Worker Node here. One belongs to EC2 Managed Node Group with name format **`ip-<PRIVATE-IP>.ap-southeast-1.compute.internal`** and the other belongs to Fargate Profile with name format **``fargate-ip-<PRIVATE-IP>.ap-southeast-1.compute.internal``**. Let take note the name of Fargate Profile 's Node, we will describe the Application Pod to see which Node it is placing.

6. List your Application Pod again to get the Pod Name.
```
kubectl get -n ns-fcj-fp pod
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.17.clbnlbwitheksfargate.png?pc=90pt)


7. Let describe your Pod.
```
kubectl describe -n ns-fcj-fp pod <REPLACE-WITH-POD-NAME>
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.18.clbnlbwitheksfargate.png?pc=90pt)

Let compare your Fargate Profile 's Node Name and value of Node field to verify that they are match.

#### Congratulations, you had deployed application and integrated with Classic Load Balancer on Fargate Node successfully!

### Clean up
1. Execute the below command to delete your created resources on namespace **ns-fcj-fp**.
```
kubectl delete -n ns-fcj-fp -f kube-manifest
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.19.clbnlbwitheksfargate.png?pc=90pt)

2. List all resources to make sure they are deleted.
```
kubectl get deploy,pod,svc -n ns-fcj-fp
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.20.clbnlbwitheksfargate.png?pc=90pt)

3. Access to [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) again to verify the created Classic Load Balancer was deleted automatically when **fcj-app1-clb-svc** Service deleted.
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.21.clbnlbwitheksfargate.png?pc=90pt)

4. Delete created Fargate Profile. It will take you about 5 minutes to finish.
```
eksctl delete fargateprofile --name fp-app1 --cluster fcj-elb-cluster --region ap-southeast-1 --wait
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.22.clbnlbwitheksfargate.png?pc=90pt)

5. List Fargate Profile to make sure it is deleted.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1 -o yaml
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.23.clbnlbwitheksfargate.png?pc=90pt)

6. Delete created Namespace.
```
kubectl delete ns ns-fcj-fp
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.24.clbnlbwitheksfargate.png?pc=90pt)

7. List all namespace to make sure it is deleted.
```
kubectl get ns
```
![Classic Load Balancer service with Amazon EKS Cluster](../../images/3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.25.clbnlbwitheksfargate.png?pc=90pt)
---
title : "Install AWS Load Balancer Controller"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---
AWS Load Balancer Controller is a controller to help manage Elastic Load Balancers for a Kubernetes cluster.
The controller can provision the following resources:
+ An AWS Application Load Balancer when you create a Kubernetes Ingress.
+ An AWS Network Load Balancer or Classic Load Balancer when you create a Kubernetes Service of type LoadBalancer.

![Install Load Balancer Controller](../../images/4.ingresswitheks/awsloadbalancercontroller.png?pc=90pt)


To integrate your Application Pod on EC2 Managed Nodegroup with Ingress (Application Load Balanber) and Route53 Service. It's necessary to have **alb-ingress-access**, **full-ecr-access** and **external-dns-access** access permission. To do that, we will delete current Nodegroup and create another new with those accesses.

### Delete current NodeGroup
1. Execute the below command to delete current EC2 Managed NodeGroup.
```
eksctl delete nodegroup --cluster fcj-elb-cluster --region ap-southeast-1 --name fcj-elb-nodegroup --disable-eviction
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.1.installlbc.png?pc=90pt)

2. It will take you about 5 minutes to finish.
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.2.installlbc.png?pc=90pt)

3. List all Nodes in your Cluster to make sure it was deleted.
```
kubectl get nodes
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.3.installlbc.png?pc=90pt)

### Create a new NodeGroup
1. Execute the below command to create new EC2 Managed NodeGroup with **alb-ingress-access**, **full-ecr-access** and **external-dns-access** access permission.
```
eksctl create nodegroup --cluster fcj-elb-cluster --region ap-southeast-1 --name fcj-ingress-nodegroup --managed --nodes=1 --node-type=t3.large --alb-ingress-access --full-ecr-access --external-dns-access --node-private-networking
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.4.installlbc.png?pc=90pt)

2. It will take you about 5 minutes to finish.
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.5.installlbc.png?pc=90pt)


3. List your created Node.
```
kubectl get nodes -o wide
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.6.installlbc.png?pc=90pt)

### Create IAM Policy
In this step, we will create IAM policy for the AWS Load Balancer Controller that allows it to make calls to AWS APIs on your behalf.
1. At Cloud9 Terminal, download IAM Policy.
```
curl -o iam_policy_latest.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.7.installlbc.png?pc=90pt)

2. Create IAM Policy using policy downloaded.
```
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy_latest.json
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.8.installlbc.png?pc=90pt)

3. Make a note of Policy ARN as we are going to use that in next step when creating IAM Role.

![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.9.installlbc.png?pc=90pt)

### Create an IAM Role for the AWS LoadBalancer Controller and attach the Role to the Kubernetes Service Account.
1. Verify if any existing service account.
```
kubectl get sa aws-load-balancer-controller -n kube-system
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.10.installlbc.png?pc=90pt)

There is no created Service Account named **aws-load-balancer-controller** on Namespace **kube-system** on your Cluster.

2. Create IAM Role. Replace with your Policy ARN.
```
eksctl create iamserviceaccount \
  --cluster=fcj-elb-cluster \
  --region=ap-southeast-1 \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=<REPACE-WITH-YOUR-POLICY-ARN> \
  --override-existing-serviceaccounts \
  --approve
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.11.installlbc.png?pc=90pt)

3. Verify that there ia a created service account named **aws-load-balancer-controller** on Namespace **kube-system** on your Cluster.

![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.12.installlbc.png?pc=90pt)

4. Let describe the service account for more detail.
```
kubectl describe sa aws-load-balancer-controller -n kube-system 
```

![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.13.installlbc.png?pc=90pt)

5. At **Annotations** field, we can see there is a IAM Role Arn associated with the Service Account. Let copy the IAM Role name.
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.14.installlbc.png?pc=90pt)

6. Access to [IAM Role]() and search with IAM Role name to see the result.
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.15.installlbc.png?pc=90pt)

There is an IAM Role created and associated to your Service Account automatically when you created it.

### Install the AWS Load Balancer Controller
In this step, we will install the AWS Load Balancer Controller by using Helm.
1. Install Helm.
```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 > get_helm.sh
chmod 700 get_helm.sh
./get_helm.sh
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.16.installlbc.png?pc=90pt)

2. Check Helm version.
```
helm version
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.17.installlbc.png?pc=90pt)


3. Install the AWS Load Balancer Controller with Helm. Replace with your Cluster's VPC ID.
```
# Add the eks-charts repository.
helm repo add eks https://aws.github.io/eks-charts
# Update your local repo to make sure that you have the most recent charts.
helm repo update
# Install the AWS Load Balancer Controller.
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=fcj-elb-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=ap-southeast-1 \
  --set vpcId=<REPLACE-WITH-CLUSTER-VPC-ID> \
  --set image.repository=602401143452.dkr.ecr.ap-southeast-1.amazonaws.com/amazon/aws-load-balancer-controller
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.18.installlbc.png?pc=90pt)

4. Verify that the controller is installed and Webhook Service created
```
kubectl -n kube-system get deployment aws-load-balancer-controller
kubectl -n kube-system get svc aws-load-balancer-webhook-service
kubectl get pods -n kube-system
```

![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.19.installlbc.png?pc=90pt)

There are some created resources:
+ A created Deployment named **aws-load-balancer-controller**.
+ A created Webhook Service named **aws-load-balancer-webhook-service**.
+ Two created Pods named **aws-load-balancer-controller-xxxxxxxxxx-xxxxx**.


5. Let describe a created Pod to see that the **AWS_ROLE_ARN** parameter is generated IAM Role ARN when you create Service Account.

![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.20.installlbc.png?pc=90pt)

You had installed the AWS Load Balancer Controller successful.

### Create Ingress Class

1. Create a new working directory for this section.
```
cd ~
cd environment
mkdir ingress
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.21.installlbc.png?pc=90pt)

2. Create a new directory to store ingress class manifest file.
```
mkdir ingress/ingress-class
ls ingress
```

![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.22.installlbc.png?pc=90pt)

3. Create a manifest file named **ingress-class.yaml** inside **ingress/ingress-class**.
```
touch ingress/ingress-class/ingress-class.yaml
ls ingress/ingress-class/
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.23.installlbc.png?pc=90pt)

4. Open file **ingress-class.yaml**, paste the below code. Then save it.
```
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: my-aws-ingress-class
  annotations:
    ingressclass.kubernetes.io/is-default-class: "true"
spec:
  controller: ingress.k8s.aws/alb
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.24.installlbc.png?pc=90pt)

5. Execute the below command to create Ingress Class.
```
kubectl apply -f ingress/ingress-class
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.25.installlbc.png?pc=90pt)

6. List your created Ingress Class.
```
kubectl get ingressclass
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.26.installlbc.png?pc=90pt)

There is a created Ingress Class named **my-aws-ingress-class**.

7. Describe your created Ingress Class.
```
kubectl describe ingressclass my-aws-ingress-class
```
![Install Load Balancer Controller](../../images/4.ingresswitheks/4.1.installlbc/4.1.27.installlbc.png?pc=90pt)
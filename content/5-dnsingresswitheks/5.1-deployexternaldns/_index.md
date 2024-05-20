---
title : "Deploy ExternalDNS Service"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

In this section, we will Deploy ExternalDNS Service on Amazon EKS Cluster.

Inspired by Kubernetes DNS, Kubernetes' cluster-internal DNS server, ExternalDNS makes Kubernetes resources discoverable via public DNS servers. Like KubeDNS, it retrieves a list of resources (Services, Ingresses, etc.) from the Kubernetes API to determine a desired list of DNS records. Unlike KubeDNS, however, it's not a DNS server itself, but merely configures other DNS providers accordingly—e.g. AWS Route 53. 

In a broader sense, ExternalDNS allows you to control DNS records dynamically via Kubernetes resources in a DNS provider-agnostic way.


![Deploy Load Balancer Service on Amazon EKS](../../images/5.externaldns/external-dns.png?pc=60pt)

1. Create a new working directory for this section.
```
mkdir ingress/externaldns
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.1.deployexternaldns.png?pc=90pt)


2. Create a new directory to store manifest files for deploying ExternalDNS service.
```
mkdir ingress/externaldns/externaldns-deployment
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.2.deployexternaldns.png?pc=90pt)


### Create Service Account.
#### Create IAM Policy.
1. Go to [IAM Policy](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/policies). Click on **Create policy**.

![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.3.deployexternaldns.png?pc=90pt)
2. Navigate to **JSON** tab.
3. Paste the below code to **Policy editor**.
4. Click on **Next**.
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "route53:ChangeResourceRecordSets"
      ],
      "Resource": [
        "arn:aws:route53:::hostedzone/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "route53:ListHostedZones",
        "route53:ListResourceRecordSets"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.4.deployexternaldns.png?pc=90pt)

5. Input ```AllowExternalDNSUpdates``` as **Name**.
6. Input ```Allow access to Route53 Resources for ExternalDNS``` as **Description**.
7. Click on **Create policy**.

![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.5.deployexternaldns.png?pc=90pt)

8. Save the **ARN** of policy to use later.
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.6.deployexternaldns.png?pc=90pt)

#### Create Service Account.
1. Create new Namespace for this section.
```
kubectl create ns fcj-external-dns-ingress-ns
kubectl get ns fcj-external-dns-ingress-ns
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.7.deployexternaldns.png?pc=90pt)


2. Create Service Account on created Namespace. Replace with your created Policy Arn.
```
eksctl create iamserviceaccount \
    --name external-dns \
    --namespace fcj-external-dns-ingress-ns \
    --cluster fcj-elb-cluster \
    --region ap-southeast-1 \
    --attach-policy-arn <REPLACE-WITH-YOUR-CREATED-POLICY-ARN> \
    --approve \
    --override-existing-serviceaccounts
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.8.deployexternaldns.png?pc=90pt)

3. List created Service Account.
```
kubectl get sa external-dns -n fcj-external-dns-ingress-ns
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.9.deployexternaldns.png?pc=90pt)

4. Verify that IAM Service Account is created.
```
eksctl get iamserviceaccount --cluster fcj-elb-cluster --region ap-southeast-1
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.10.deployexternaldns.png?pc=90pt)

Save the **ROLE ARN** of IAM Service Account **external-dns** to use later.

### Create ExternalDNS manifest file.
1. Create a file named **01-Deploy-ExternalDNS.yaml** inside **ingress/externaldns/externaldns-deployment**.
```
touch ingress/externaldns/externaldns-deployment/01-Deploy-ExternalDNS.yaml
ls ingress/externaldns/externaldns-deployment
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.11.deployexternaldns.png?pc=90pt)

2. Open file **01-Deploy-ExternalDNS.yaml**, paste the below code. Replace with your created IAM Service Account **external-dns** Arn. Then save it.
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: external-dns
  # If you're using Amazon EKS with IAM Roles for Service Accounts, specify the following annotation.
  # Otherwise, you may safely omit it.
  annotations:
    # Substitute your account ID and IAM service role name below. #Change-1: Replace with your IAM ARN Role for extern-dns
    eks.amazonaws.com/role-arn: <REPLACE-WITH-YOUR-IAM-SERVICE-ACCOUNT-ARN>
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: external-dns
rules:
- apiGroups: [""]
  resources: ["services","endpoints","pods"]
  verbs: ["get","watch","list"]
- apiGroups: ["extensions","networking.k8s.io"]
  resources: ["ingresses"]
  verbs: ["get","watch","list"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list","watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: external-dns-viewer
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: external-dns
subjects:
- kind: ServiceAccount
  name: external-dns
  namespace: fcj-external-dns-ingress-ns
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: external-dns
spec:
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: external-dns
  template:
    metadata:
      labels:
        app: external-dns 
    spec:
      serviceAccountName: external-dns
      containers:
      - name: external-dns
        image: k8s.gcr.io/external-dns/external-dns:v0.10.2
        args:
        - --source=service
        - --source=ingress
        - --provider=aws
        - --aws-zone-type=public # only look at public hosted zones (valid values are public, private or no value for both)
        - --registry=txt
        - --txt-owner-id=my-hostedzone-identifier
      securityContext:
        fsGroup: 65534 # For ExternalDNS to be able to read Kubernetes and AWS token files
```

![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.12.deployexternaldns.png?pc=90pt)

### Deploy ExternalDNS
1. Execute the below command to deploy ExternalDNS.
```
kubectl apply -f ingress/externaldns/externaldns-deployment -n fcj-external-dns-ingress-ns
```
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.13.deployexternaldns.png?pc=90pt)

2. List all resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl get all -n fcj-external-dns-ingress-ns
``` 
![Deploy ExternalDNS Service](../../images/5.externaldns/5.1.deployexternaldns/5.1.14.deployexternaldns.png?pc=90pt)


---
title : "Triển khai ExternalDNS Service"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

Trong phần này, chúng ta sẽ triển khai ExternalDNS Service trên Amazon EKS Cluster.

Lấy cảm hứng từ Kubernetes DNS, máy chủ DNS nội bộ theo cụm của Kubernetes, InternalDNS giúp các tài nguyên Kubernetes có thể được khám phá thông qua các máy chủ DNS công cộng. Giống như KubeDNS, nó truy xuất danh sách tài nguyên (Dịch vụ, Truy cập, v.v.) từ API Kubernetes để xác định danh sách bản ghi DNS mong muốn. Tuy nhiên, không giống như KubeDNS, bản thân nó không phải là máy chủ DNS mà chỉ định cấu hình các nhà cung cấp DNS khác cho phù hợp—ví dụ: AWS Route53.

Theo nghĩa rộng hơn, ExternalDNS cho phép bạn kiểm soát các bản ghi DNS một cách linh hoạt thông qua tài nguyên Kubernetes theo cách không phân biệt nhà cung cấp DNS.


![Deploy Load Balancer Service on Amazon EKS](../../../images/5.externaldns/external-dns.png?pc=60pt)

1. Tạo một thư mục làm việc cho phần này.
```
mkdir ingress/externaldns
```
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.1.deployexternaldns.png?pc=90pt)


2. Tạo một thư mục mới để chứa các tệp Manifest cho việc triển khai ExternalDNS Service.
```
mkdir ingress/externaldns/externaldns-deployment
```
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.2.deployexternaldns.png?pc=90pt)


### Tạo Service Account.
#### Tạo IAM Policy.
1. Đi đến [IAM Policy](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/policies). Nhấn **Create policy**.

![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.3.deployexternaldns.png?pc=90pt)
2. Chuyển đến thanh **JSON**.
3. Dán đoạn code bên dưới vào **Policy editor**.
4. Nhấn **Next**.
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
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.4.deployexternaldns.png?pc=90pt)

5. Nhập ```AllowExternalDNSUpdates``` là **Name**.
6. Nhập ```Allow access to Route53 Resources for ExternalDNS``` là **Description**.
7. Nhấn **Create policy**.

![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.5.deployexternaldns.png?pc=90pt)

8. Lưu lại **ARN** của policy để sử dụng sau.
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.6.deployexternaldns.png?pc=90pt)

#### Tạo Service Account.
1. Tạo Namespace mới cho phần này.
```
kubectl create ns fcj-external-dns-ingress-ns
kubectl get ns fcj-external-dns-ingress-ns
```
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.7.deployexternaldns.png?pc=90pt)


2. Tạo Service Account trên Namespace đã tạo. Thay thế với Policy Arn đã tạo.
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
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.8.deployexternaldns.png?pc=90pt)

3. Liệt kê Service Account đã tạo.
```
kubectl get sa external-dns -n fcj-external-dns-ingress-ns
```
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.9.deployexternaldns.png?pc=90pt)

4. Xác minh rằng IAM Service Account đã tạo.
```
eksctl get iamserviceaccount --cluster fcj-elb-cluster --region ap-southeast-1
```
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.10.deployexternaldns.png?pc=90pt)

Lưu **ROLE ARN** của IAM Service Account **external-dns** để sử dụng sau.

### Tạo tệp ExternalDNS manifest.
1. Tạo tệp **01-Deploy-ExternalDNS.yaml** bên trong **ingress/externaldns/externaldns-deployment**.
```
touch ingress/externaldns/externaldns-deployment/01-Deploy-ExternalDNS.yaml
ls ingress/externaldns/externaldns-deployment
```
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.11.deployexternaldns.png?pc=90pt)

2. Mở tệp **01-Deploy-ExternalDNS.yaml**, dán đoạn code bên dưới. Thay thế với IAM Service Account đã tạo **external-dns** Arn. Sau đó lưu lại.
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

![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.12.deployexternaldns.png?pc=90pt)

### Triển khai ExternalDNS
1. Thực thi câu lệnh bên dưới để triển khai ExternalDNS.
```
kubectl apply -f ingress/externaldns/externaldns-deployment -n fcj-external-dns-ingress-ns
```
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.13.deployexternaldns.png?pc=90pt)

2. Liệt kê tất cả tài nguyên bên trong Namespace **fcj-external-dns-ingress-ns**.
```
kubectl get all -n fcj-external-dns-ingress-ns
``` 
![Triển khai ExternalDNS Service](../../../images/5.externaldns/5.1.deployexternaldns/5.1.14.deployexternaldns.png?pc=90pt)


---
title : "Cài đặt AWS Load Balancer Controller"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---
AWS Load Balancer Controller là một bộ điều khiển giúp quản lý Elastic Load Balancers cho một Kubernetes cluster.
Bộ điều khiển có thể cung cấp các tài nguyên dưới đây:
+ Một AWS Application Load Balancer khi bạn tạo Kubernetes Ingress.
+ Một AWS Network Load Balancer hoặc Classic Load Balancer khi bạn tạo một Kubernetes Service với **Type** là LoadBalancer.

![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/awsloadbalancercontroller.png?pc=90pt)

Để tích hợp Application Pod trên EC2 Managed Nodegroup với Ingress (Application Load Balanber) và Route53. Các quyền truy cập **alb-ingress-access**, **full-ecr-access** và **external-dns-access** là cần thiết. Để thực hiện điều đó, chúng ta sẽ xóa Nodegroup hiện tại và tạo lại cái mới với các quyền đó.

### Xóa NodeGroup hiện tại
1.Thực thi câu lệnh bên dưới để xóa EC2 Managed NodeGroup hiện tại.
```
eksctl delete nodegroup --cluster fcj-elb-cluster --region ap-southeast-1 --name fcj-elb-nodegroup --disable-eviction
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.1.installlbc.png?pc=90pt)

2. Sẽ mất khoảng 5 phút để hoàn tất.
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.2.installlbc.png?pc=90pt)

3. Liệt kê lại tất cả các Node bên trong Cluster để đảm bảo nó đã xóa.
```
kubectl get nodes
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.3.installlbc.png?pc=90pt)

### Tạo một NodeGroup mới.
1.Thực thi câu lệnh bên dưới để tạo mọt EC2 Managed NodeGroup mới với quyền truy cập **alb-ingress-access**, **full-ecr-access** và **external-dns-access**.
```
eksctl create nodegroup --cluster fcj-elb-cluster --region ap-southeast-1 --name fcj-ingress-nodegroup --managed --nodes=1 --node-type=t3.large --alb-ingress-access --full-ecr-access --external-dns-access --node-private-networking
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.4.installlbc.png?pc=90pt)

2. Sẽ mất khoảng 5 phút để hoàn tất.
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.5.installlbc.png?pc=90pt)


3. Liệt kê Node đã tạo.
```
kubectl get nodes -o wide
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.6.installlbc.png?pc=90pt)

### Tạo IAM Policy
Ở bước này, chúng ta sẽ tạo IAM Policy cho AWS Load Balancer Controller để cho phép nó thực hiện gọi đến AWS API thay bạn.
1. Tại cửa sổ lệnh Cloud9, tải xuống IAM Policy.
```
curl -o iam_policy_latest.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.7.installlbc.png?pc=90pt)

2. Tạo IAM Policy sử dụng policy vừa tải xuống.
```
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy_latest.json
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.8.installlbc.png?pc=90pt)

3. Lưu lại Policy ARN vì chúng ta sẽ sử dụng khi tạo IAM Role.

![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.9.installlbc.png?pc=90pt)

### Tạo một IAM Role cho AWS LoadBalancer Controller và gán Role cho Kubernetes Service Account.
1. Kiểm tra xem Service Account có tồn tại.
```
kubectl get sa aws-load-balancer-controller -n kube-system
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.10.installlbc.png?pc=90pt)

Không có Service Account được tạo tên là **aws-load-balancer-controller** trong Namespace **kube-system** ở Cluster.

2. Tạo IAM Role. Thay thế với Policy ARN.
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
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.11.installlbc.png?pc=90pt)

3. Kiểm tra có một Service Account tên **aws-load-balancer-controller** trong Namespace **kube-system** ở Cluster.

![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.12.installlbc.png?pc=90pt)

4. Khám phá chi tiết Service Account.
```
kubectl describe sa aws-load-balancer-controller -n kube-system 
```

![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.13.installlbc.png?pc=90pt)

5. Tại mục **Annotations**, có thể thấy có một IAM Role Arn liên kết tới Service Account. Sao chép tên IAM Role.
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.14.installlbc.png?pc=90pt)

6. Truy cập [IAM Role]() và tìm kiếm với tên IAM Role để kiểm tra kết quả.
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.15.installlbc.png?pc=90pt)


Có một IAM Role được tạo và kết nối với Service Account tự động khi bạn tạo nó.

### Cài đặt AWS Load Balancer Controller
Ở bước này, chúng ta sẽ cài đặt AWS Load Balancer Controller bằng Helm.
1. Cài đặt Helm.
```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 > get_helm.sh
chmod 700 get_helm.sh
./get_helm.sh
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.16.installlbc.png?pc=90pt)

2. Kiểm tra phiên bản Helm.
```
helm version
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.17.installlbc.png?pc=90pt)


3. Cài đặt AWS Load Balancer Controller với Helm. Thay thế với VPC ID của Cluster.
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
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.18.installlbc.png?pc=90pt)

4. Kiểm tra bộ điều khiển đã được cài đặt và Webhook Service đã được tạo.
```
kubectl -n kube-system get deployment aws-load-balancer-controller
kubectl -n kube-system get svc aws-load-balancer-webhook-service
kubectl get pods -n kube-system
```

![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.19.installlbc.png?pc=90pt)

Các tài nguyên được tạo ra:
+ Một Deployment tên **aws-load-balancer-controller**.
+ Một Webhook Service tên **aws-load-balancer-webhook-service**.
+ Hai Pods tên **aws-load-balancer-controller-xxxxxxxxxx-xxxxx**.

5. Hãy khám phá Pod để tạo để thấy rằng thông số **AWS_ROLE_ARN** là IAM Role ARN đã được tạo ra khi bạn tạo Service Account.

![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.20.installlbc.png?pc=90pt)

Bạn đã cài đặt AWS Load Balancer Controller thành công.

### Tạo Ingress Class

1. Tạo một thư mục làm việc mới cho phần này.
```
cd ~
cd environment
mkdir ingress
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.21.installlbc.png?pc=90pt)

2. Tạo một thư mục mới để chứa tệp manifest Ingress class.
```
mkdir ingress/ingress-class
ls ingress
```

![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.22.installlbc.png?pc=90pt)

3. Tạo một tệp manifest tên **ingress-class.yaml** bên trong **ingress/ingress-class**.
```
touch ingress/ingress-class/ingress-class.yaml
ls ingress/ingress-class/
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.23.installlbc.png?pc=90pt)

4.Mở tệp **ingress-class.yaml**, dán đoạn code bên dưới. Sau đó lưu lại.
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
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.24.installlbc.png?pc=90pt)

5. Thực thi câu lệnh bên dưới để tạo Ingress Class.
```
kubectl apply -f ingress/ingress-class
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.25.installlbc.png?pc=90pt)

6. Liệt kê Ingress Class được tạo.
```
kubectl get ingressclass
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.26.installlbc.png?pc=90pt)

Có một Ingress Class được tạo tên **my-aws-ingress-class**.

7. Khám phá Ingress Class được tạo.
```
kubectl describe ingressclass my-aws-ingress-class
```
![Cài đặt Load Balancer Controller](../../../images/4.ingresswitheks/4.1.installlbc/4.1.27.installlbc.png?pc=90pt)
---
title : "Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2</b> "
---

1. Di chuyển tất cả tài nguyên của phần trước đến một thư mục mới tên **ManagedNodeGroup**.
```
mkdir ManagedNodeGroup
mv app-deployment.yaml ManagedNodeGroup/
mv ClassicLoadBalancer.yaml ManagedNodeGroup/
ls ManagedNodeGroup
```

![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.1.clbnlbwitheksfargate.png?pc=90pt)

2. Tạo thư mục mới tên **Fargate** cho phần này.
```
mkdir Fargate
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.2.clbnlbwitheksfargate.png?pc=90pt)


### Tạo Fargate Profile
1. Di chuyển đến thư mục **Fargate** và tạo một thư mục tên **FargateProfile**.
```
cd Fargate
mkdir FargateProfile
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.3.clbnlbwitheksfargate.png?pc=90pt)

2. Tạo tệp **fargate-profiles.yml** bên trong **FargateProfile**.
```
touch FargateProfile/fargate-profiles.yml
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.4.clbnlbwitheksfargate.png?pc=90pt)

3. Mở tệp **fargate-profiles.yml**, dán đoạn code bên dưới. Sau đó lưu lại.
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
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.5.clbnlbwitheksfargate.png?pc=90pt)

4. Để tạo Fargate Profile, bạn cần tạo namespace khớp với giá trị thông số đã cung cấp **fargateProfiles.selectors.namespace** trong tệp **fargate-profiles.yml**.
```
kubectl create ns ns-fcj-fp
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.6.clbnlbwitheksfargate.png?pc=90pt)

5. Liệt kê các namespace đã tạo.
```
kubectl get ns
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.7.clbnlbwitheksfargate.png?pc=90pt)

6. Tạo Fargate Profile.
```
eksctl create fargateprofile -f FargateProfile/fargate-profiles.yml
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.8.clbnlbwitheksfargate.png?pc=90pt)

7. Liệt kê Fargate Profile đã tạo.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1 -o yaml
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.9.clbnlbwitheksfargate.png?pc=90pt)


### Tạo tệp Manifest
Chúng ta sẽ sử dụng lại các tệp Manifest trong phần trước.
1. Tạo thư mục tên **kube-manifest**.
```
mkdir kube-manifest
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.10.clbnlbwitheksfargate.png?pc=90pt)

2. Sao chép các tệp ở **ManagedNodeGroup** đến **Fargate/kube-manifest**.
```
cd ..
cp -r ManagedNodeGroup/* Fargate/kube-manifest
cd Fargate
ls kube-manifest
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.11.clbnlbwitheksfargate.png?pc=90pt)

### Triển khai tài nguyên
1. Thực thi câu lệnh dưới để triển khai tài nguyên lên namespace **ns-fcj-fp** đã được định nghĩa ở Fargate Profile.
```
kubectl apply -n ns-fcj-fp -f kube-manifest
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.12.clbnlbwitheksfargate.png?pc=90pt)

2. Liệt kê tất cả tài nguyên đã tạo. Sẽ mất khoảng 3 phút cho việc tạo tài nguyên thành công.
```
kubectl get -n ns-fcj-fp deploy,pod,svc
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.13.clbnlbwitheksfargate.png?pc=90pt)

Có các tài nguyên được tạo trên Namespace **fcj-app1**:
+ Một Deployment tên **fcj-app1-deployment**.
+ Một Pod tên **fcj-app1-deployment-7d9d4f7c44-lbqc6** với **STATUS** là **Running**.
+ Một Service tên **fcj-app1-clb-svc** với **TYPE** là **LoadBalancer** và **EXTERNAL-IP** là **a40e3cb6afdf84f60b8b8064284ad7cf-785887277.ap-southeast-1.elb.amazonaws.com**.

3. Hãy truy cập [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3). Có một Load Balancer được tạo với **Type** là **classic** là **DNS name** khớp với **EXTERNAL-IP** của **fcj-app1-clb-svc** Service.
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.14.clbnlbwitheksfargate.png?pc=90pt)

4. Hãy truy cập đường dẫn ```http://<YOUR-EXTERNAL-IP>:8080``` để kiểm tra kết quả.
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.15.clbnlbwitheksfargate.png?pc=90pt)

Ứng dụng của bạn đã được triển khai với Classic Load Balancer thành công.

5. Hãy liệt kê tất cả Node đang chạy bên trong Cluster.
```
kubectl get nodes
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.16.clbnlbwitheksfargate.png?pc=90pt)

Chúng có có 2 loại Worker Node ở đây. Một loại thuộc về EC2 Managed Node Group với định dạng tên là **`ip-<PRIVATE-IP>.ap-southeast-1.compute.internal`** và loại còn lại thuộc về Fargate Profile với định dạng tên là **``fargate-ip-<PRIVATE-IP>.ap-southeast-1.compute.internal``**. Hãy lưu lại tên của Fargate Profile Node. Chúng ta sẽ khám phá  Application Pod để thấy Node nào mà nó đang chạy trên.

6. Liệt kê Application Pod và lấy Pod Name.
```
kubectl get -n ns-fcj-fp pod
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.17.clbnlbwitheksfargate.png?pc=90pt)


7. Khám phá Pod.
```
kubectl describe -n ns-fcj-fp pod <REPLACE-WITH-POD-NAME>
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.18.clbnlbwitheksfargate.png?pc=90pt)

Hãy so sánh Fargate Profile Node Name và giá trị của mục Node để xác minh rằng chúng khớp nhau.

#### Chúc mừng, bạn đã triển khai và tích hợp Classic Load Balancer trên Fargate Node thành công.

### Dọn dẹp
1. Thực thi câu lệnh bên dưới để xóa các tài nguyên đã tạo trên namespace **ns-fcj-fp**.
```
kubectl delete -n ns-fcj-fp -f kube-manifest
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.19.clbnlbwitheksfargate.png?pc=90pt)

2. Liệt kê lại tất cả tài nguyên để đảm bảo rằng chúng đã được xóa.
```
kubectl get deploy,pod,svc -n ns-fcj-fp
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.20.clbnlbwitheksfargate.png?pc=90pt)

3. Truy cập [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:v=3) để xác minh rằng Classic Load Balancer vừa tạo đã được tự động xóa khi **fcj-app1-clb-svc** Service bị xóa.
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.21.clbnlbwitheksfargate.png?pc=90pt)

4. Xóa Fargate Profile. Sẽ mất khoảng 5 phút để hoàn tất.
```
eksctl delete fargateprofile --name fp-app1 --cluster fcj-elb-cluster --region ap-southeast-1 --wait
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.22.clbnlbwitheksfargate.png?pc=90pt)

5. Liệt Fargate Profile để đảm bảo nó đã được xóa.
```
eksctl get fargateprofile --cluster fcj-elb-cluster --region ap-southeast-1 -o yaml
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.23.clbnlbwitheksfargate.png?pc=90pt)

6. Xóa Namespace đã tạo.
```
kubectl delete ns ns-fcj-fp
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.24.clbnlbwitheksfargate.png?pc=90pt)

7. Liệt kê namespace để đảm bảo nó đã được xóa
```
kubectl get ns
```
![Dịch vụ Classic Load Balancer với Amazon EKS Cluster Fargate Profile](../../../images//3.clbnlbwitheks/3.2.clbnlbwitheksfargate/3.2.25.clbnlbwitheksfargate.png?pc=90pt)
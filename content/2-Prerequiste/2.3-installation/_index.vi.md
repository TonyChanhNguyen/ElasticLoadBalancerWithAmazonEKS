---
title : "Cài đặt công cụ"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---
Trong bài thực hành này, chúng ta sẽ cài đặt các công cụ cần thiết: awscli, kubectl và eksctl.
### Nâng cấp awscli
1. Sao chép và dán dòng lệnh dưới đây vào cửa sổ lệnh Cloud8 để nâng cấp awscli.
```
sudo pip install --upgrade awscli && hash -r
```
![Cài đặt công cụ](../../../images/2.prerequisites/2.3.installation/2.3.1.installation.png?pc=60pt)


### Cài đặt kubectl
1. Tại **cửa sổ lệnh Cloud9**, thực hiện câu lệnh này để cài đặt **kubectl**.
+ Nâng cấp các gói tin trong máy chủ.
```bash
sudo yum update
```
+ Cài đặt kubectl.
```bash
curl -LO https://dl.k8s.io/release/v1.30.0/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
2. Kiểm tra phiên bản của **kubectl**.
```
kubectl version --client
```

![Cài đặt K8S](../../../images/2.prerequisites/2.3.installation/2.3.2.installation.png?pc=60pt)

### Cài đặt eksctl

1. Tại **cửa sổ lệnh Cloud9**, thực hiện câu lệnh này để cài đặt eksctl.
+ Tải xuống và giải nén bản phát hành mới nhất của eksctl bằng lệnh sau.
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
```

+ Di chuyển nhị phân được trích xuất sang /usr/local/bin.
```
sudo mv /tmp/eksctl /usr/local/bin
```

+ Kiểm thử việc cài đặt đã thành công bằng câu lệnh
```
eksctl version
```
![Cài đặt Amazon EKS](../../../images/2.prerequisites/2.3.installation/2.3.3.installation.png?pc=60pt)
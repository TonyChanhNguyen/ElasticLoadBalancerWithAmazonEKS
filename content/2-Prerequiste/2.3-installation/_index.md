---
title : "Installation"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---
In this workshop, we will install necessary tools: awscli, kubectl and eksctl.
### Upgrade awscli
1. Copy and paste the command below into Terminal of Cloud9 Workspace to upgrade awscli.
```
sudo pip install --upgrade awscli && hash -r
```
![Installation](../../images/2.prerequisites/2.3.installation/2.3.1.installation.png?pc=60pt)


### Install kubectl
1. At **Cloud9 Terminal**, execute those command to install **kubectl**.
+ Update the instance packages.
```bash
sudo yum update
```
+ Install kubectl.
```bash
curl -LO https://dl.k8s.io/release/v1.30.0/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
2. Check version of **kubectl**.
```
kubectl version --client
```

![Install K8S](../../images/2.prerequisites/2.3.installation/2.3.2.installation.png?pc=60pt)

### Install eksctl

1. At Cloud9 terminal, execute those command to install eksctl.
+ Download and extract the latest release of eksctl with the following command.
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
```

+ Move the extracted binary to /usr/local/bin.
```
sudo mv /tmp/eksctl /usr/local/bin
```

+ Test that your installation was successful with the following command
```
eksctl version
```
![Install Amazon EKS](../../images/2.prerequisites/2.3.installation/2.3.3.installation.png?pc=60pt)
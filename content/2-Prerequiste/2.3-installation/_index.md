---
title : "Installation"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---
In this workshop, we will create a basic application with NodeJS and Express framework. So we need to check the version of them and install if they are not existing.
### Upgrade awscli
1. Copy and paste the command below into Terminal of Cloud9 Workspace to upgrade awscli.
```
sudo pip install --upgrade awscli && hash -r
```
![Installation](../../images/2.prerequisites/2.3.installation/2.3.1.installation.png?pc=60pt)


### Check version of npm and node
1. Copy and Paste the command below into Terminal of Cloud9 Workspace to check version of npm and node.
```
npm version
```
![Installation](../../images/2.prerequisites/2.3.installation/2.3.2.installation.png?pc=60pt)
At you see, npm and node had been installed. Now we will install Express framework by using npm.

### Install Express framework
1. Copy and Paste the command below into Terminal of Cloud9 Workspace to install Express framework.
```
npm install express --save
```
![Installation](../../images/2.prerequisites/2.3.installation/2.3.3.installation.png?pc=60pt)

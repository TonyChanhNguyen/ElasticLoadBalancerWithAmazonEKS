---
title : "Cài đặt ứng dụng"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---
Trong bài thực hành này, chúng ta sẽ tạo một ứng dụng đơn giản với NodeJS và Express. Vì thế chúng ta cần kiểm tra phiên bản của chúng và tải xuống nếu chúng chưa được cài đặt.
### Nâng cấp awscli
1. Sao chép và dán dòng lệnh dưới đây vào cửa sổ lệnh của Cloud9 Workspace để nâng cấp awscli.
```
sudo pip install --upgrade awscli && hash -r
```
![Cài đặt ứng dụng](../../../images/2.prerequisites/2.3.installation/2.3.1.installation.png?pc=60pt)


### Kiểm tra phiên bản của npm và node
1. Sao chép và dán dòng lệnh dưới đây vào cửa sổ lệnh của Cloud9 Workspace để kiểm tra phiên bản của npm và node.
```
npm version
```
![Cài đặt ứng dụng](../../../images/2.prerequisites/2.3.installation/2.3.2.installation.png?pc=60pt)
Bạn có thể thấy, npm và node đã được cài đặt. Giờ chúng ta sẽ cài đặt Express framework bằng npm.

### Cài đặt Express framework
1. Sao chép và dán dòng lệnh dưới đây vào cửa sổ lệnh của Cloud9 Workspace để cài đặt Express framework.
```
npm install express --save
```
![Cài đặt ứng dụngion](../../../images/2.prerequisites/2.3.installation/2.3.3.installation.png?pc=60pt)

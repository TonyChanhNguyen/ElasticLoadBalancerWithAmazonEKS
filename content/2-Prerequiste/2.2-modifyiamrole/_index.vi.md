---
title : "Cấu hình IAM role"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---
Trong bước này, chúng ta sẽ tạo một IAM Role và gán nó cho máy chủ làm việc.

### Tạo IAM role
1. Nhấn [IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1) để chuyển hướpg đến dịch vụ IAM.
2. Nhấn **Role**.
3. Nhấn **Create role**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.1.iamrole.png?pc=90pt)
4. Ở mục **Trusted entity type**, chọn **AWS service**.
5. Ở mục **Service or use case**, chọn **EC2**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.2.iamrole.png?pc=90pt)
6. Sau đó, nhấn **Next**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.3.iamrole.png?pc=90pt)

7. Ở mục **Permissions policies**, chọn policy tên **AdministratorAccess**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.4.iamrole.png?pc=90pt)
8. Sau đó, nhấn **Next**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.5.iamrole.png?pc=90pt)

9. Tại trang **Name, review, and create**, nhập ```eksworkspace-administrator``` ở mục **Role name** .
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.6.iamrole.png?pc=90pt)

10. Sau đó, cuộn xuống cuối trang và nhấn **Create role**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.7.iamrole.png?pc=90pt)

### Gán role cho máy chủ làm việc
1. Tại trang **AWS Cloud9**, nhấn **Manage EC2 instance**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.8.iamrole.png?pc=90pt)
2. Bạn sẽ thấy máy chủ làm việc đã được tạo. Sau đó, nhấn chọn nó.
3. Nhấn **Action**.
4. Nhấn **Security**.
5. Nhấn **Modify IAM role**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.9.iamrole.png?pc=90pt)

6. Chọn Role **eksworkspace-administrator** vừa được tạo ở bước trên.
7. Sau đó, nhấn **Update IAM role**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.10.iamrole.png?pc=90pt)

8. IAM Role mới đã được cập nhật thành công.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.11.iamrole.png?pc=90pt)

### Cập nhật cấu hình Cloud9

{{% notice info %}}
Cloud9 sẽ quản lý thông tin xác thực IAM tự dộng. Cấu hình mặc định này hiện không phù hợp với xác thực EKS thông qua IAM, chúng ta sẽ cần phải vô hiệu hóa tính năng này và sử dụng IAM Role.
{{% /notice %}}

1. Tại trang **AWS Cloud9**, nhấn **AWS Cloud9**.
2. Chọn **Preferences**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.12.iamrole.png?pc=90pt)

3. Tại mục **AWS Settings**, vô hiệu hóa **AWS managed temporary credentials**.
![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.13.iamrole.png?pc=90pt)

4. Để đảm bảo rằng các thông tin xác thực tạm thời không còn lưu trữ trong **Cloud9**, chúng ta sẽ xóa tất cả thông tin xác thực đang tồn tại với dòng lệnh bên dưới.
```
rm -vf ${HOME}/.aws/credentials
```

![Tạo IAM role](../../../images/2.prerequisites/2.2.iamrole/2.2.14.iamrole.png?pc=90pt)
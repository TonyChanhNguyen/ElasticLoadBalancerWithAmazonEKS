---
title : "Tạo ứng dụng"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 2.5 </b> "
---

Trong chương này, chúng ta sẽ tải xuống một ứng dụng mẫu **FCJ Management**.

### Tải xuống ứng dụng.
1. Kiểm tra phiên bản của Git.
```
git version
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.1.createapp.png?pc=60pt)

2. Nâng cấp Git lên phiên bản mới nhất.
```
sudo yum update git
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.2.createapp.png?pc=60pt)

3. Tạo và di chuyển đến thư mục làm việc.
```
mkdir fcj-user-management
cd fcj-user-management
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.3.createapp.png?pc=60pt)

4. Tải xuống ứng dụng.
```
git clone https://github.com/First-Cloud-Journey/000004-EC2.git
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.4.createapp.png?pc=60pt)

5. Liệt kê tất cả tài nguyên của ứng dụng.
```
ls
ls 000004-EC2
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.5.createapp.png?pc=60pt)

6. Đổi tên thư mục **000004-EC2** thành **Application**.
```
mv 000004-EC2 Application
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.6.createapp.png?pc=60pt)

7. Liệt kê lại tất cả tài nguyên của ứng dụng.
```
ls
ls Application
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.7.createapp.png?pc=60pt)
8. Di chuyển đến thư mục ứng dụng và cài đặt các gói phụ thuộc của ứng dụng trên **package.json**.
```
cd Application
npm install
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.8.createapp.png?pc=60pt)
9. Khởi chạy ứng dụng.
```
node app.js
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.9.createapp.png?pc=60pt)

Ứng dụng sẽ trả về lỗi **Fail to connect to database**, bởi vì chưa có CSDL MySQL được tạo và cung cấp cho ứng dụng để sử dụng.

Bây giờ chúng ta sẽ đóng gói ứng dụng thành Container Image bằng Docker. CSDL MySQL sẽ được cung cấp ở các phần tiếp theo.


### Đóng gói ứng dụng
1. Kiểm tra phiên bản của Docker.
```
docker version
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.10.createapp.png?pc=60pt)

2. Tạo tệp **Dockerfile**.
```
touch Dockerfile
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.11.createapp.png?pc=60pt)

3. Mở tệp **Dockerfile**, dán đoạn mã code bên dưới và lưu lại.
```cmd
# Use an official Node.js runtime as a parent image
FROM node:13-alpine
# Set the working directory in the container
WORKDIR app
# Copy package.json and package-lock.json to the working directory
COPY package*.json ./
# Install dependencies
RUN npm install
# Copy the rest of the application code
COPY . .
# Expose the port the app runs on
EXPOSE 5000
# Command to run the application
CMD ["npm","start"]
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.12.createapp.png?pc=60pt)

4. Tạo tệp **.dockerignore** để làm giảm dung lượng cho Container Image.
```
touch .dockerignore
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.13.createapp.png?pc=60pt)

5. Nhấn vào biểu tượng **Settings**, chọn **Show Hidden Files** để nhìn thấy tệp ẩn **.dockerignore**.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.14.createapp.png?pc=60pt)

6. Mở tệp **.dockerignore**, dán đoạn mã code bên dưới và lưu lại.
```
.git
node_modules
Dockerfile
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.15.createapp.png?pc=60pt)

7. Liệt kê tất cả Image trong máy chủ. Đảm bảo rằng không có Image nào tên **fcj-management** bởi vì chúng ta sẽ dùng tên này để đặt cho Container Image.
```
docker images
```

8. Xây dựng ứng dụng thành Container Image.
```
docker build -t fcj-management:v1 .
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.16.createapp.png?pc=60pt)

9. Sau khi quy trình này hoàn tất. Hãy liệt kê lại tất cả Image trong máy chủ làm việc.
```
docker images
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.17.createapp.png?pc=60pt)

Container Image tên **fcj-management** với nhãn **v1** được tạo thành công từ ứng dụng **FCJ Management**. Bây giờ hãy sử dụng Docker để triển khai ứng dụng từ Container Image vừa tạo cho mục đích kiểm thử.

10. Thực thi câu lệnh bên dưới để triển khai ứng dụng từ Container Image vừa tạo.
```
docker run -d --name testing-application -e PORT=8080 -p 8080:8080 fcj-management:v1
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.18.createapp.png?pc=60pt)

11. Liệt kê các process đang chạy bên trong máy chủ làm việc.
```
docker ps
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.20.createapp.png?pc=60pt)

12. Kiểm tra Log của ứng dụng.
```
docker logs testing-application
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.21.createapp.png?pc=60pt)

Lỗi xuất hiện trên log cũng là **Fail to connect to database**. Có nghĩa Container Image đã được xây dụng thành công.

13. Hãy xóa tất cả Process đang chạy.
```
docker stop testing-application
docker rm testing-application
docker ps -a
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.22.createapp.png?pc=60pt)


### Đẩy Container Image lên DockerHub
Trong bước này, chúng ta sẽ đẩy Container Image vừa tạo lên DockerHub cho Amazon EKS Cluster có thể kéo về để chạy Pod. Vì thế chúng ta sẽ không đào sâu vào việc làm thế nào để sử dụng DockerHub, chi tiết truy cập [DockerHub Docs](https://docs.docker.com/).

1. Đăng nhập vào [DockerHub](https://hub.docker.com/repository/docker) với tài khoản của bạn.
2. Tạo một Repository tên ```fcj-management```.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.23.createapp.png?pc=60pt)

3. Đi đến [DockerHub Security](https://hub.docker.com/settings/security) để tạo Access Token mới cho máy chủ làm việc Cloud9 đăng nhập.
4. Nhấn **New Access Token**.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.24.createapp.png?pc=60pt)

5. Nhập tại **Access Token Description** là ```Access Token for FCJ Workshop```.
6. Giữ mặc định **Access Permissions** (với quyền Read, Write, Delete).
7. Sau đó, nhấn **Generate**.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.25.createapp.png?pc=60pt)

8. Lưu mã Access Token lại để sử dụng sau.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.26.createapp.png?pc=60pt)

9. Trở lại cửa sổ lệnh Cloud9, đăng nhập vào DockerHub.
```
docker login -u firstcloudjourneypcr
```

10. Nhập Access Token nếu bị hỏi **Password**.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.27.createapp.png?pc=60pt)


Để đẩy Container Image lên DockerHub repository. Repository của Container Image phải khớp với tên Repository trên DockerHub. 

11. Để làm điều đó, chúng ta sẽ gắn nhãn cho Container Image.
```
docker tag fcj-management:v1 firstcloudjourneypcr/fcj-management:v1
```

![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.28.createapp.png?pc=60pt)

12. Liệt kê tất cả Image trong máy chủ làm việc.
```
docker images
```

![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.29.createapp.png?pc=60pt)

Cho một Image mới được chép lại với Repository tên là **firstcloudjourneypcr/fcj-management**, nhãn và **v1**.
13. Bây giờ, hãy đẩy Container Image lên DockerHub.
```
docker push firstcloudjourneypcr/fcj-management:v1
```

![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.30.createapp.png?pc=60pt)

14. Đi đến DockerHub Repository **firstcloudjourneypcr/fcj-management** để kiểm tra.

![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/2.5.31.createapp.png?pc=60pt)

Có một Image mới vừa được đẩy lên DockerHub Repository với tag là **v1**.
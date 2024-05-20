---
title : "Tạo ứng dụng"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 2.5 </b> "
---

Trong phần này, chúng ta sẽ tạo một ứng dụng đơn giản với 2 phiên bản v1 và v2. Sau đó, docker hóa và đẩy chúng lên DockerHub cho EKS Cluster sử dụng.

### Tạo ứng dụng v1
1. Tại cửa sổ lệnh Cloud9, nhập câu lệnh bên dưới để tạo một thư mục mới cho ứng dụng.
```
mkdir app
cd app
```
2. Khởi tạo ứng dụng.
```
npm init
```
3. Nhấn **Enter** để bỏ qua các bước này và xác nhận **Yes** để hoàn tất.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.1.createapp.png?pc=60pt)
4. Tạo một tệp tên **index.js**.
```
touch index.js
```
5. Mở tệp **index.js** và thực hiện mã code.
```javascript
import express from 'express'

const app = express()

app.get('/',(req, res) => {
    res.json("Hello world from FCJ Workshop V1!")
})

app.listen(8080, ()=> {
    console.log("application running on 8080")
})
```

6. Tại cửa sổ lệnh Cloud9, cài đặt **express** framework.
```
npm i express
```
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.1.1.createapp.png?pc=60pt)

7. Lưu tệp và khởi chạy ứng dụng.
```
node index.js
```

8. Nhưng chúng ta sẽ gặp mã lỗi như bên dưới.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.2.createapp.png?pc=60pt)

9. Để sửa lỗi này, mở tệp **package.json** và thêm đoạn định nghĩa này. Sau đó lưu lại.
```
"type":"module",
```
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.3.createapp.png?pc=60pt)

10. Bây giờ, hãy chạy lại ứng dụng.
```
node index.js
```

11. Ứng dụng đã khởi chạy trên port 8080.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.4.createapp.png?pc=60pt)

Bây giờ, chúng ta sẽ truy cập vào ứng dụng để kiểm tra kết quả.

12. Nhấn **Share**.
13. Sao chép **IP Address** tại **Application**.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.5.createapp.png?pc=60pt)

14. Truy cập vào ứng dụng với đường dẫn ```http://<REPLACE_YOUR_IP>:8080```.
15. Nhưng ứng dụng không thể truy cập được.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.6.createapp.png?pc=60pt)

Nguyên nhân là bởi vì Security group của máy chủ làm việc vẫn chưa mở Port 8080.

16. Nhấn vào biểu tượng **R** và chọn **Manage EC2 Instance** để đi đến máy chủ làm việc.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.7.createapp.png?pc=60pt)

17. Tại trang **Instances**, chọn máy chủ làm việc của bạn.
18. Chuyển đến thanh **Security**.
19. Nhấn **Security Group**.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.8.createapp.png?pc=60pt)

Tại trang **Inbound rules**, bạn có thể thấy chưa có quy tắc nào được định nghĩa.

20. Nhấn **Edit inbound rules**.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.9.createapp.png?pc=60pt)
21. Tại trang **Edit inbound rules**, nhấn **Add rule**.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.10.createapp.png?pc=60pt)
22. Định nghĩa quy tắc với thông số:
+ **Type** là **Custom TCP**.
+ **Port range** là **8080**.
+ **Source** là **Anywhere-IPv4**.

23. Sau đó, nhấn **Save rules**.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.11.createapp.png?pc=60pt)

24. Bây giờ, hãy truy cập lại ứng dụng để kiểm tra kết quả.
![Tạo ứng dụng cơ bản](../../../images/2.prerequisites/2.5.createapp/2.4.12.createapp.png?pc=60pt)

### Docker hóa ứng dụng v1
1. Tạo tệp tên **Dockerfile**.
```
touch Dockerfile
```
![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.1.createdockerfile.png?pc=90pt)

2. Mở **Dockerfile**.
![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.2.createdockerfile.png?pc=90pt)

3. Nhập đoạn code bên dưới.
```Dockerfile
FROM node:13-alpine

#configure working directory
WORKDIR /app

COPY package.json ./

RUN npm install

#bundle the source code

COPY . ./

EXPOSE 8080

CMD ["node","index.js"]


```
![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.3.createdockerfile.png?pc=90pt)

*Chú ý*: 
+ Tại bước **COPY package.json ./**, Docker sẽ sao chép tệp **package.json** tới thư mục **/app**, sau đó thực hiện **RUN npm install** để cài đặt tất cả các gói hổ trợ được định nghĩa trong tệp **package.json** và lưu chúng vào thư mục **node_modules**.

+ Tại bước **COPY . ./**, Docker sẽ sao chép tất cả tài nguyên trên máy chủ làm việc đến thư mục **/app** - bao gồm thư mục **node_modules** và tệp **Dockerfile**. Các tệp và thư mục này không cần thiết để sao chép lại lên thư mục làm việc. Vì thể chúng ta có thể tạo **.dockerignore** để liệt kê các thư mục và tệp nào không cần sao chép vào thư mục làm việc.


4. Tạo tệp tên **.dockerignore** bằng câu lệnh.
```
touch .dockerignore
```
![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.4.createdockerfile.png?pc=80pt)

Bạn sẽ không thấy **.dockerignore** ở đâu, vì Cloud9 nhận định **.dockerignore** là tệp ẩn.

5. Nhấn **Setting**.
6. Chọn **Show Hidden Files**.

![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.5.createdockerfile.png?pc=90pt)

7. Mở **.dockerignore** và nhập các giá trị bên dưới.
```
node_modules
Dockerfile
```
![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.6.createdockerfile.png?pc=90pt)

8. Chạy câu lệnh này để liệt kê tất cả Image đang tồn tại bên trong máy chủ làm việc của bạn.
```
docker images
```
![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.7.createdockerfile.png?pc=90pt)

Không có Image nào tồn tại.
9. Hãy xây dụng container image.
```
docker build -t fcj-application:v1 .
```
![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.8.createdockerfile.png?pc=90pt)

10. Liệt kê Image lại lần nữa.
```
docker images
```
![Docke hóa ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.9.createdockerfile.png?pc=90pt)

Container image đã được xây dựng với nhãn **v1** thành công.

### Tạo ứng dụng v2
1. Mở tệp **index.js** và thay thế **res.json("Hello world from FCJ Workshop V1!")** thành **res.json("Hello world from FCJ Workshop V2!")** ở dòng 6.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.10.createdockerfile.png?pc=90pt)

2. Khởi chạy ứng dụng để kiểm tra kết quả.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.11.createdockerfile.png?pc=90pt)

3. Truy cập vào đường dẫn của ứng dụng.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.12.createdockerfile.png?pc=90pt)

### Docker hóa ứng dụng v2
1. Hãy xây dụng Container image.
```
docker build -t fcj-application:v2 .
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.13.createdockerfile.png?pc=90pt)

2. Liệt kê Image lần nữa.
```
docker images
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.14.createdockerfile.png?pc=90pt)

### Đẩy container image lên DockerHub.
1. Truy cập và đăng nhập vào [DockerHub](https://hub.docker.com/) với tài khoản của bạn.
2. Tạo một Repository cho bài thực hành này.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.15.createdockerfile.png?pc=90pt)

3. Nhập các thông tin được yêu cầu:
+ **Name**: ```fcj-elbeks-workshop-basicapp```.
+ **Description**: ```Store container image for Amazon ELB with Amazon EKS Cluster Workshop```.
Sau đó nhấn **Create**.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.16.createdockerfile.png?pc=90pt)


Kế tiếp, chúng ta sẽ tạo một Access Token được sử dụng để đăng nhập vào DockerHub từ máy chủ làm việc.

4. Nhấn avatar của bạn (Góc trên phải của trang).

5. Nhấn **My Account**.

6. Nhấn **Security**.

7. Cuối cùng, nhấn **New Access Token**.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.17.createdockerfile.png?pc=90pt)

8. Điền **Access Token Description**.
9. Nhấn **Generate**.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.18.createdockerfile.png?pc=90pt)

10. Lưu trữ Access token được tạo ra.
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.19.createdockerfile.png?pc=90pt)

11. Trở lại cửa sổ lệnh của Cloud9. Nhập câu lệnh bên dưới để đăng nhập và cung cấp Access Token khi được yêu cầu.
```
docker login -u <REPLACE-YOUR-DOCKERHUB-USERNAME>
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.20.createdockerfile.png?pc=90pt)


Để đẩy container image lên repository, Repository của container image phải khớp với định dạng repository của DockerHub `<YOUR_USER_NAME>/<YOUR_REPOSITORY_NAME>`. Vì thế bây giờ chúng ta cần sử dụng `docker image tag command` để sao chép và chuẩn hóa định dạng repository của container image trong máy chủ làm việc.

12. Thực hiện câu lệnh này để dán nhãn cho container image phiên bản v1 và v2.
```
docker image tag fcj-application:v1 firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v1
docker image tag fcj-application:v2 firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v2
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.21.createdockerfile.png?pc=90pt)


13. Liệt kê tất cả image.
```
docker images
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.22.createdockerfile.png?pc=90pt)


Bây giờ, có 2 ontainer image mới là firstcloudjourneypcr/fcj-elbeks-workshop-basicapp với nhãn là v1 và firstcloudjourneypcr/fcj-elbeks-workshop-basicapp với nhãn là v2. Kế tiếp, hãy đẩy các container image này lên DockerHub.

14. Thực thi câu lệnh này để đẩy container image cả phiên bản v1 và v2 lên DockerHub.
```
docker push firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v1
docker push firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v2
```
![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.23.createdockerfile.png?pc=90pt)

15. Trở về Docker Hub Repository để kiểm tra kết quả. Có 2 container image vừa được đẩy lên.

![Tạo ứng dụng](../../../images/2.prerequisites/2.5.createapp/3.2.24.createdockerfile.png?pc=90pt)

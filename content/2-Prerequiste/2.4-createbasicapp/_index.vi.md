---
title : "Tạo ứng dụng đơn giản"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---
Ở bước này, chúng ta sẽ tạo một ứng dụng đơn giản sử dụng NodeJS và Express framework.

1. Tại cửa sổ lệnh Cloud9, nhập dòng lệnh bên dưới để tạo thư mục cho ứng dụng.
```
mkdir app
cd app
```
2. Khởi tạo ứng dụng.
```
npm init
```
3. Nhấn **Enter** để bỏ qua các bước và xác nhận **Yes** để kết thúc.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.1.createapp.png?pc=60pt)
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

6. Lưu tệp và khởi chạy ứng dụng.
```
node index.js
```

7. Nhưng bạn sẽ gặp lỗi bên dưới.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.2.createapp.png?pc=60pt)

9. Để giải quyết, mở tệp **package.json** và thêm định nghĩa bên dưới. Sau đó lưu lại
```
"type":"module",
```
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.3.createapp.png?pc=60pt)

10. Bây giờ, chạy lại ứng dụng.
```
node index.js
```

11. Ứng dụng của bạn đã chạy trên cổng 8080.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.4.createapp.png?pc=60pt)

Bây giờ, chúng ta cần truy cập vào ứng dụng để thấy kết quả.

12. Nhấn **Share**.
13. Sao chép **IP Address** ở mục **Application**.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.5.createapp.png?pc=60pt)

14. Truy cập ứng dụng bằng URL ```http://<REPLACE_YOUR_IP>:8080```.
15. Ứng dụng của bạn không thể truy cập.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.6.createapp.png?pc=60pt)

Nguyên nhân và bởi vì security group của máy chủ làm việc vẫn chưa mở cổng 8080.

16. Nhấn vào biểu tượng **R** và chọn **Manage EC2 Instance** để trở lại máy chủ làm việc.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.7.createapp.png?pc=60pt)

17. Tại trang **Instances**, chọn máy chủ làm việc của bạn.
18. Chuyển sang mục **Security**.
19. Nhấn **Security Group**.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.8.createapp.png?pc=60pt)

Tại trang **Inbound rules**, bạn có thể thấy chưa có quy tắc nào được định nghĩa.

20. Nhấn **Edit inbound rules**.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.9.createapp.png?pc=60pt)
21. Tại trang **Edit inbound rules**, nhấn **Add rule**.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.10.createapp.png?pc=60pt)
22. Định nghĩa quy tắc với thông số:
+ **Type** là **Custom TCP**.
+ **Port range** là **8080**.
+ **Source** là **Anywhere-IPv4**.

23. Sau đó, nhấn **Save rules**.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.11.createapp.png?pc=60pt)

24. Bây giờ, hãy truy cập lại ứng dụng và kiểm tra kết quả.
![Create basic application](../../../images/2.prerequisites/2.4.createapp/2.4.12.createapp.png?pc=60pt)

#### Chúc mừng, ứng dụng đã hoạt động.
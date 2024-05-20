---
title : "SSL và SSL Redirect"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 5.4 </b> "
---

Trong phần này, chúng ta sẽ tạo một chứng chỉ SSL cho ứng dụng. Sau đó, chuyển hướng tất cả lưu lượng yêu cầu HTTP đến cổng HTTPS.

## Tích hợp chứng chỉ SSL lên ứng dụng của bạn.

#### Create a SSL Certificate in Certificate Manager
1. Đi đến [AWS Certificate Manager](https://ap-southeast-1.console.aws.amazon.com/acm/home?region=ap-southeast-1#/welcome). Nhấn **Request a certificate**.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.2.sslandsslredirect.png?pc=90pt)

2. Tại **Request certificate**, nhấn **Next**.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.3.sslandsslredirect.png?pc=90pt)

3. Nhập tên miền với định dạng ```*.<YOUR-DOMAIN-NAME>``` tại mục **Fully qualified domain name**.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.4.sslandsslredirect.png?pc=90pt)

4. Giữ mặc định ở các mục còn lại. Sau đó nhấn **Request**.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.5.sslandsslredirect.png?pc=90pt)

5. **Certificate** của bạn đã được tạo nhưng ở trạng thái **Pending validation**.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.6.sslandsslredirect.png?pc=90pt)

6. Hãy tạo bản ghi mới bên trong Route53 để xác thực. Nhấn **Create records in Route 53**.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.7.sslandsslredirect.png?pc=90pt)

7. Nhấn **Create records**.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.8.sslandsslredirect.png?pc=90pt)

8. Đi đến [Route 53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1), nhấn **Host Zone** của bạn để thấy có một bản ghi mới được tạo bởi **AWS Certificate Manager**.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.9.sslandsslredirect.png?pc=90pt)

9. Trở lại [AWS Certificate Manager](https://ap-southeast-1.console.aws.amazon.com/acm/home?region=ap-southeast-1#/certificates/list) để thấy trạng thái chứng chỉ bây giờ là **Issued**.  
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.10.sslandsslredirect.png?pc=90pt)

{{% notice info %}}
Sẽ mất khoảng 10 phút để xác thực thành công.
{{% /notice %}}

10. Lưu lại ARN để sử dụng sau.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.11.sslandsslredirect.png?pc=90pt)

#### Tạo tệp Manifest.
1. Tạo thư mục mới cho phần này.
```
mkdir ingress/externaldns/ssl-and-ssl-redirect
ls ingress/externaldns
```
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.1.sslandsslredirect.png?pc=90pt)

2. Chúng ta sẽ sử dụng lại các tài nguyên của phần trước. Hãy sao chép tất cả tài nguyên bên trong **ingress/externaldns/host-header-routing-ingress** đến **ingress/externaldns/ssl-and-ssl-redirect**.
```
cp ingress/externaldns/host-header-routing-ingress/* ingress/externaldns/ssl-and-ssl-redirect
ls ingress/externaldns/ssl-and-ssl-redirect
```
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.12.sslandsslredirect.png?pc=90pt)

3. Mở tệp **05-ALB-ingress.yaml**, thêm đoạn chú thích bên dưới. Thay thế ```<REPLACE-WITH-YOUR-CERTIFICATE-ARN>``` với Arn của chứng chỉ đã tạo và lưu lại.

```
## SSL Settings
alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS":443}, {"HTTP":80}]'
alb.ingress.kubernetes.io/certificate-arn: <REPLACE-WITH-YOUR-CERTIFICATE-ARN>
```
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.13.sslandsslredirect.png?pc=90pt)

#### Triển khai tài nguyên
1. Triển khai tài nguyên lên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/ssl-and-ssl-redirect -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.14.sslandsslredirect.png?pc=90pt)

2. Đi đến [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:) để xác minh có một Application Load Balancer vừa được tạo.

![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.15.sslandsslredirect.png?pc=90pt)

3. Nhấn vào nó và xác minh rằng có một **Listener** mới được tạo cho phương thức **HTTPS** trên cổng **443**, và **Default SSL/TLS certificate** là chứng chỉ đã tạo ở bước trên.

![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.16.sslandsslredirect.png?pc=90pt)

4. Nhấn vào để xác minh **Listener rules**. 
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.17.sslandsslredirect.png?pc=90pt)

+ Với lưu lượng HTTPS đến cổng 443 với **HTTP Host Header** là ```firstcloudjourney-app1.<YOUR-DOMAIN-NAME>```, nó sẽ chuyển đến **Target Group** của App1. 
+ Với lưu lượng HTTPS đến cổng 443 với **HTTP Host Header** là ```firstcloudjourney-app2.<YOUR-DOMAIN-NAME>```, nó sẽ chuyển đến **Target Group** của App2. 

### Kiểm tra kết quả.
1. Truy cập ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.18.sslandsslredirect.png?pc=90pt)


Chúng ta có thể kết nối tới ứng dụng App1 nhưng kết nối không được bảo mật.

2. Truy cập ```https://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.19.sslandsslredirect.png?pc=90pt)

Với phương thức HTTPS, kết nối tới ứng dụng App1 được bảo mật.

3. Tương tự, truy cập ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2``` và ```https://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2``` để kiểm tra kết quả.

![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.20.sslandsslredirect.png?pc=90pt)

![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.21.sslandsslredirect.png?pc=90pt)

## Chuyển định tuyến lưu lượng truy cập HTTP đến cổng HTTPS.
#### Chỉnh sửa tệp Manifest.
1. Mở tệp **05-ALB-ingress.yaml**, thêm đoạn chú thích bên dưới. Sau đó lưu lại.
```
# SSL Redirect Setting
alb.ingress.kubernetes.io/ssl-redirect: '443'   
```
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.22.sslandsslredirect.png?pc=90pt)

#### Triển khai lại tài nguyên.
1. Triển khai lại tài nguyên trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/ssl-and-ssl-redirect -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
Chỉ có Ingress được cấu hình.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.23.sslandsslredirect.png?pc=90pt)

2. Truy cập Application Load Balancer trên [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:) lần nữa để kiểm tra sự thay đổi.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.24.sslandsslredirect.png?pc=90pt)

Sự thay đổi xuất hiện trên **HTTP:80**, **Rules** giảm từ **3** xuống **1**.

3. Nhấn **HTTP:80** để kiểm tra sự thay đổi.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.25.sslandsslredirect.png?pc=90pt)

Tất cả **Listener Rules** đã bị xóa, có một quy tắc mới được tạo sẽ chuyển hướng đến **Port 443** khi có lưu lượng yêu cầu.


#### Kiểm tra kết quả.
1. Truy cập ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.26.sslandsslredirect.png?pc=90pt)

Lưu lượng truy cập sẽ được tự động chuyển hướng đến **Port 443** với **HTTPS protocol**.

2. Truy cập ```https://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.27.sslandsslredirect.png?pc=90pt)

Lưu lượng truy cập đến **Port 443** với **HTTPS protocol** vẫn có thể được truy cập như bình thường.

3. Tương tự, tuy cập ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2``` và ```https://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2``` để kiểm tra kết quả.
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.28.sslandsslredirect.png?pc=90pt)

#### Dọn dẹp.
1. Xóa tất cả tài nguyên đã tạo
```
kubectl delete -f ingress/externaldns/ssl-and-ssl-redirect -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![SSL và SSL Redirect](../../../images/5.externaldns/5.4.sslandsslredirect/5.4.29.sslandsslredirect.png?pc=90pt)

Sẽ giữ lại các tài nguyên liên quan đến dịch vụ **external-dns** và **SSL certificate** trên **AWS Certificate Manager** cho các phần tiếp theo.
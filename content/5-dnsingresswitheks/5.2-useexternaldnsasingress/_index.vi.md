---
title : "Sử dụng ExternalDNS như Ingress Service"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---


### Tạo tệp Manifest.
1. Tạo một thư mục làm việc mới.
```
mkdir ingress/externaldns/externaldns-ingress
ls ingress/externaldns
```

![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.1.externaldnsingress.png?pc=90pt)

2. Sao chép tất cả tài nguyên trong **ingress/context-based-routing-ingress** đến **ingress/externaldns/externaldns-ingress**.
```
cp ingress/context-based-routing-ingress/* ingress/externaldns/externaldns-ingress
ls ingress/externaldns/externaldns-ingress
```

![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.2.externaldnsingress.png?pc=90pt)


3. Mở tệp **05-ALB-ingress.yaml**, thêm đoạn chú thích bên dưới. Thay thế với **DNS Name** của bạn.
```
# External DNS - For creating a Record Set in Route53
external-dns.alpha.kubernetes.io/hostname: <REPLACE-WITH-YOUR-DNSNAME>
```
![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.3.externaldnsingress.png?pc=90pt)

### Triển khai tài nguyên.
1. Triển khai tài nguyên trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/externaldns-ingress -n fcj-external-dns-ingress-ns
```
![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.4.externaldnsingress.png?pc=90pt)

2. Liệt kê tất cả tài nguyên đã tạo trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl get all -n fcj-external-dns-ingress-ns
```
![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.5.externaldnsingress.png?pc=90pt)

3. Truy cập [Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1#) nhấn vào **Host Zone** của bạn để xác minh có một bản ghi DNS mới **firstcloudjourney** được tạo.

![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.6.externaldnsingress.png?pc=90pt)

### Kiểm tra kết quả.
1. Truy cập ```http://<YOUR-DNS-NAME>/app1```.
![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.7.externaldnsingress.png?pc=90pt)

2. Truy cập ```http://<YOUR-DNS-NAME>/app2```.
![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.8.externaldnsingress.png?pc=90pt)

#### Chúc mừng, bạn đã triển khai ứng dụng với DNS Name thành công.

### Dọn dẹp.
1. Xóa các tài nguyên đã tạo.
```
kubectl delete -f ingress/externaldns/externaldns-ingress -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.9.externaldnsingress.png?pc=90pt)

Sẽ giữ lại các tài nguyên liên quan đến dịch vụ **external-dns** cho các phần tiếp theo.

2. Đi đến [Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1#) nhấn vào **Host Zone** của bạnđể xác minh rằng DNS Record **firstcloudjourney** đã tự động xóa khi Ingress Service xóa.
![Sử dụng ExternalDNS như Ingress Service](../../../images/5.externaldns/5.2.externaldnsingress/5.2.10.externaldnsingress.png?pc=90pt)


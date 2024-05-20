---
title : "Name Based Virtual Host Routing"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 5.3 </b> "
---

Trong phần này, chúng ta sẽ triển khai định tuyến Host Header sử dụng Ingress.

### Tạo tệp Manifest.
1. Tạo thư mục làm việc mới cho phần này.
```
mkdir ingress/externaldns/host-header-routing-ingress
ls ingress/externaldns
```
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.1.namebasedhostroutingingress.png?pc=90pt)

2. Chúng ta sẽ sử dụng lại các tài nguyên của phần trước. Hãy sao chép tất cả tài nguyên bên trong **ingress/externaldns/externaldns-ingress** vào **ingress/externaldns/host-header-routing-ingress**.
```
cp ingress/externaldns/externaldns-ingress/* ingress/externaldns/host-header-routing-ingress
ls ingress/externaldns/host-header-routing-ingress
```
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.2.namebasedhostroutingingress.png?pc=90pt)

3. Mở tệp **05-ALB-ingress.yaml**, xóa tất cả định nghĩa của nó và thay thế bằng đoạn code bên dưới. Sau đó, thay thế ```<REPLACE-WITH-YOUR-HOST-HEADER-APP1>``` và ```<REPLACE-WITH-YOUR-HOST-HEADER-APP2>``` với **Host Header** của bạn cho mỗi ứng dụng.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fcj-host-header-based-routing-ingress
  annotations:
    # Load Balancer Name
    alb.ingress.kubernetes.io/load-balancer-name: fcj-host-header-based-routing
    # Ingress Core Settings
    alb.ingress.kubernetes.io/scheme: internet-facing
    # Health Check Settings
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
    alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
    alb.ingress.kubernetes.io/success-codes: '200'
    alb.ingress.kubernetes.io/healthy-threshold-count: '2'
    alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'

spec:
  ingressClassName: my-aws-ingress-class # Ingress Class
  rules:
  - host: <REPLACE-WITH-YOUR-HOST-HEADER-APP1>
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: fcj-app1-nodeport-service
            port:
              number: 80
  - host: <REPLACE-WITH-YOUR-HOST-HEADER-APP2>
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: fcj-app2-nodeport-service
            port:
              number: 80
```

![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.3.namebasedhostroutingingress.png?pc=90pt)

### Triển khai tài nguyên.
1. Triển khai tài nguyên trên Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/host-header-routing-ingress -n fcj-external-dns-ingress-ns
```
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.4.namebasedhostroutingingress.png?pc=90pt)

2. Liệt kê tất cả tài nguyên đã tạo.
```
kubectl get all -n fcj-external-dns-ingress-ns
```
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.5.namebasedhostroutingingress.png?pc=90pt)

3. Truy cập [Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1#) nhấn vào **Host Zone** của bạn để xác minh có 2 DNS Records mới **firstcloudjourney-app1** và **firstcloudjourney-app2** được tạo ra.
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.6.namebasedhostroutingingress.png?pc=90pt)

### Kiểm tra kết quả.
1. Truy cập ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.7.namebasedhostroutingingress.png?pc=90pt)

2. Khi bạn khi cập ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app2```. Kết quả trả về là thất bại.
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.8.namebasedhostroutingingress.png?pc=90pt)

3. Truy cập ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2```.
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.9.namebasedhostroutingingress.png?pc=90pt)

4. Khi bạn khi cập ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app1```. Kết quả trả về là thất bại.
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.10.namebasedhostroutingingress.png?pc=90pt)

#### Chúc mừng, bạn đã triển khai ứng dụng với định tuyến tên miền dựa trên máy chủ ảo thành công.

### Dọn dẹp.
1. Xóa các tài nguyên đã tạo.
```
kubectl delete -f ingress/externaldns/host-header-routing-ingress -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.11.namebasedhostroutingingress.png?pc=90pt)

Sẽ giữ lại các tài nguyên liên quan đến dịch vụ **external-dns** cho các phần tiếp theo.

2. Đi đến [Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1#) nhấn vào **Host Zone** để xác mình DNS Records được tạo **firstcloudjourney-app1** và **firstcloudjourney-app2** tự động xóa khi Ingress Service bị xóa.
![Name Based Virtual Host Routing](../../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.12.namebasedhostroutingingress.png?pc=90pt)
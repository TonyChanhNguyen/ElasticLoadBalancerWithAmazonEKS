---
title : "Cung cấp động"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---

### Tạo các tệp manifest
1. Tại cửa sổ lệnh Cloud9, tạo một thư mục tên ```dynamic-provision/kube-manifest```.
```
mkdir -p dynamic-provision/kube-manifest
```
2. Tạo một tệp tên **storageclass.yaml** bên trong **dynamic-provision/kube-manifest**.
```
touch dynamic-provision/kube-manifest/storageclass.yaml
```
3. Mở tệp **storageclass.yaml**, dán đoạn mã code bên dưới. Sau đó lưu lại.
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.1.dynamicprovision.png?pc=60pt)

4. Tạo một tệp tên **pv-claim.yaml** bên trong **dynamic-provision/kube-manifest**.
```
touch dynamic-provision/kube-manifest/pv-claim.yaml
```
5. Mở tệp **pv-claim.yaml**, dán đoạn mã code bên dưới. Sau đó lưu lại.
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: ebs-sc
  resources:
    requests:
      storage: 10Gi
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.2.dynamicprovision.png?pc=60pt)

6. Tạo một tệp tên **pod.yaml** bên trong **dynamic-provision/kube-manifest**.
```
touch dynamic-provision/kube-manifest/pod.yaml
```

7. Mở tệp **pod.yaml**, dán đoạn mã code bên dưới. Sau đó lưu lại.
```
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  containers:
  - name: app
    image: centos
    command: ["/bin/sh"]
    args: ["-c", "while true; do echo $(date -u) >> /data/out.txt; sleep 5; done"]
    volumeMounts:
    - name: persistent-storage
      mountPath: /data
  volumes:
  - name: persistent-storage
    persistentVolumeClaim:
      claimName: ebs-claim
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.3.dynamicprovision.png?pc=60pt)

### Triển khai tài nguyên
1. Tại cửa sổ lệnh Cloud9, thực thi câu lệnh dưới.
```
kubectl apply -f dynamic-provision/kube-manifest
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.4.dynamicprovision.png?pc=60pt)

### Kiểm tra kết quả
1. Liệt kê tất cả tài nguyên đã tạo.
```
kubectl get sc,pvc,pod
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.5.dynamicprovision.png?pc=60pt)

Có một Pod tên **app** với trạng thái là **Running**, một Persistent Volume Claim tên **ebs-claim** với trạng thái là **Bound** là dung lượng là **10Gi** và một Storage Class tên **ebs-sc** với Provider là **ebs.csi.aws.com**.
Storage Class sẽ tự động tạo một EBS Volume với kích cỡ ổ đĩa được định nghĩa tại **spec.resources.requests.storage** (10Gi)..

2. Đi đến [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes) để kiểm tra kết quả.
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.6.dynamicprovision.png?pc=60pt)

Có một EBS Volume mới với Availability Zone giống với của Node.

3. Chúng ta sẽ xác minh Pod viết dữ liệu vào ổ đĩa được cung cấp động thành công.
```
kubectl exec app -- cat /data/out.txt
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.7.dynamicprovision.png?pc=60pt)

Có nhiều dữ liệu được viết vào tệp **/data/out.txt**. Hãy lưu lại nội dung của ba dữ liệu đầu tiên để so sánh sau.
Trong trường hợp này là **Mon May 6 19:58:43 UTC 2024**, **Mon May 6 19:58:48 UTC 2024** và **Mon May 6 19:58:53 UTC 2024**.

Bây giờ, chúng ta sẽ xóa Pod sau đó tạo lại một cái khác để kiểm tra dữ liệu trong ổ đĩa EBS đã được cố định với Pod mới.

4. Xóa Pod hiện tại **app**.
```
kubectl delete pod app
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.8.dynamicprovision.png?pc=60pt)
5. Liệt kê tất cả các Pod để đảm bảo rằng chúng đã được xóa.
```
kubectl get pod
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.9.dynamicprovision.png?pc=60pt)
6. Tạo một Pod mới.
```
kubectl apply -f dynamic-provision/kube-manifest
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.10.dynamicprovision.png?pc=60pt)
7. Liệt kê Pod đã tạo.
```
kubectl get pod
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.11.dynamicprovision.png?pc=60pt)
8. Xác minh dữ liệu bên trong **/data/out.txt** lần nữa.
```
kubectl exec app -- cat /data/out.txt
```
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.12.dynamicprovision.png?pc=60pt)
Bạn có thể thấy, ba dữ liệu đầu tiên trong **/data/out.txt** hiện vẫn là **Mon May 6 19:58:43 UTC 2024**, **Mon May 6 19:58:48 UTC 2024** và **Mon May 6 19:58:53 UTC 2024**. Dữ liệu được giữ nguyên trong khi Pod được thay thế bằng cái khác.

### Dọn dẹp
1. Thực thi câu lệnh bên dưới để xóa tất cả tài nguyên đã tạo.
```
kubectl delete -f dynamic-provision/kube-manifest
```

![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.13.dynamicprovision.png?pc=60pt)

2. Kiểm tra tất cả tài nguyên đã được xóa thành công.
```
kubectl get sc,pvc,pod
```

![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.14.dynamicprovision.png?pc=60pt)

Tất cả đã được xóa.
Bởi vì Amazon EBS Volume được tạo tĩnh bởi Storage Class. Nên khi Storage Class bị xóa, Amazon EBS Volume cũng sẽ bị loại bỏ.

3. Đi đến [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes) để kiểm tra kết quả.
![Cung cấp động](../../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.15.dynamicprovision.png?pc=60pt)

Amazon EBS Volume đã bị loại bỏ.
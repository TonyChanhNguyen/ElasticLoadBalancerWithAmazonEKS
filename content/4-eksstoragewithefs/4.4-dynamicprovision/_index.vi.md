---
title : "Cung cấp động"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4.4 </b> "
---

### Tạo các tệp Manifest
1. Tạo thư mục cho phần **Dynamic Provisioning**.
```
mkdir -p EFS/dynamic-provision/kube-manifest
ls EFS/dynamic-provision
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.1.dynamicprovision.png?pc=60pt)

2. Tạo tệp tên **storageclass.yaml** bên trong **dynamic-provision/kube-manifest**.
```
touch EFS/dynamic-provision/kube-manifest/storageclass.yaml
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.2.dynamicprovision.png?pc=60pt)

3. Mở tệp **storageclass.yaml**, dán đoạn mã code bên dưới. Thay thế với **EFS File System ID** tại mục **fileSystemId**.
```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: efs-sc
provisioner: efs.csi.aws.com
parameters:
  provisioningMode: efs-ap
  fileSystemId: fs-0db5cdf65fc6c821d
  directoryPerms: "700"
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.3.dynamicprovision.png?pc=60pt)

4. Tạo tệp tên **pvc.yaml** bên trong **dynamic-provision/kube-manifest**.
```
touch EFS/dynamic-provision/kube-manifest/pvc.yaml
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.4.dynamicprovision.png?pc=90pt)

5. Mở tệp **pvc.yaml**, dán đoạn mã code bên dưới.
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: efs-claim
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: efs-sc
  resources:
    requests:
      storage: 5Gi
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.5.dynamicprovision.png?pc=60pt)

6. Tạo tệp tên **pod.yaml** bên trong **dynamic-provision/kube-manifest**.
```
touch EFS/dynamic-provision/kube-manifest/pod.yaml
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.6.dynamicprovision.png?pc=60pt)

7. Mở tệp **pod.yaml** , dán đoạn mã code bên dưới.
```
apiVersion: v1
kind: Pod
metadata:
  name: efs-app
spec:
  containers:
    - name: app
      image: centos
      command: ["/bin/sh"]
      args: ["-c", "while true; do echo $(date -u) >> /data/out; sleep 5; done"]
      volumeMounts:
        - name: persistent-storage
          mountPath: /data
  volumes:
    - name: persistent-storage
      persistentVolumeClaim:
        claimName: efs-claim
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.7.dynamicprovision.png?pc=60pt)

### Triển khai tài nguyên
1. Thực thi câu lệnh bên dưới để triển khai tài nguyên.
```
kubectl apply -f EFS/dynamic-provision/kube-manifest
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.8.dynamicprovision.png?pc=60pt)

2. Liệt kê tất cả tài nguyên đã được tạo.
```
kubectl get sc,pvc,pod
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.9.dynamicprovision.png?pc=60pt)

{{% notice important %}}
**STATUS** của **Persistent Volume Claim (pvc)** phải là **Bound**, và của **Pod** phải là **Running**.
{{% /notice %}}

3. Đi đến [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems).
4. Nhấn vào EFS File System của bạn.
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.10.dynamicprovision.png?pc=60pt)

5. Chuyển để thanh chuyển hướng **Access points**.
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.11.dynamicprovision.png?pc=60pt)

Bạn có thể thấy có một **Access point** vừa được tạo bới vì bạn đã chỉ định **parameters.provisioningMode** là **efs-ap** trong tệp **storageclass.yaml**.

### Kiểm tra kết quả.
1. Thực thi câu lệnh dưới để xác minh rằng dữ liệu đã được ghi vào EFS filesystem.
```
kubectl exec efs-app -- bash -c "cat data/out"
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.12.dynamicprovision.png?pc=60pt)

Có nhiều dữ liệu được viết vào tệp **/data/out**. Hãy lưu lại nội dung của ba dữ liệu đầu tiên để so sánh sau.
Trong trường hợp này là  **Wed May 8 17:52:02 UTC 2024**, **Wed May 8 17:52:07 UTC 2024** và **Wed May 8 17:52:12 UTC 2024**.

2. Bây giờ chúng ta sẽ xóa Pod hiện tại. Sau đó liệt kê tất cả các tài nguyên lần nữa để đảm bảo Pod đã được xóa.
```
kubectl delete pod efs-app
kubectl get sc,pvc,pod
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.13.dynamicprovision.png?pc=60pt)

Pod **efs-app** đã bị xóa.

3. Tạo một Pod mới.
```
kubectl apply -f EFS/dynamic-provision/kube-manifest/pod.yaml
kubectl get pod
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.14.dynamicprovision.png?pc=60pt)

4. Xác minh dữ liệu bên trong **/data/out** lần nữa.
```
kubectl exec efs-app -- bash -c "cat data/out"
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.15.dynamicprovision.png?pc=60pt)


Bạn có thể thấy, ba dữ liệu đầu tiên trong **/data/out** hiện vẫn là **Wed May 8 17:52:02 UTC 2024**, **Wed May 8 17:52:07 UTC 2024** và **Wed May 8 17:52:12 UTC 2024**. Dữ liệu được giữ nguyên trong khi Pod được thay thế bằng cái khác.

### Dọn dẹp
1. Thực thi câu lệnh bên dưới để xóa tất cả tài nguyên.
```
kubectl delete -f EFS/dynamic-provision/kube-manifest
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.16.dynamicprovision.png?pc=60pt)
2. Xác minh tất cả tài nguyên đã được xóa thành công.
```
kubectl get sc,pvc,pod
```
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.17.dynamicprovision.png?pc=60pt)

3. Đi đến [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems).
4. Chọn EFS File System của bạn.
5. Nhấn **Delete**.
![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.18.dynamicprovision.png?pc=60pt)

6. Nhập EFS File System ID để xác nhận.
7. Nhấn **Confirm** để xóa.

![Cung cấp động](../../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.19.dynamicprovision.png?pc=60pt)
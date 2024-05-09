---
title : "Cung cấp tĩnh"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---


Trong bước này, chúng ta sẽ thực hành làm thế nào để tạo và tiêu thụ một PersistentVolume từ một ổ đĩa EBS được tạo sẵn với cung cấp tĩnh.

### Kiểm tra Availability Zone của Node
Đầu tiên, chúng ta cần kiểm tra Availability Zone nào mà EC2 Managed Node Group Instance đang hoạt động.
1. Đi đến [EC2 Instances](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Instances:tag:Name=fcj-storage-cluster-fcj-storage-nodegroup-Node;v=3;$case=tags:true%5C,client:false;$regex=tags:false%5C,client:false).
2. Kiểm tra Availability Zone của nó.
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.14.staticprovision.png?pc=60pt)

Ở trường hợp này Availability Zone của Node là ap-southeast-1b, vì thế EBS Volume sẽ được tạo tại ap-southeast-1b.

### Tạo một ổ đĩa EBS
1. Đi đến [EBS Volumes](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes).
2. Bạn sẽ thấy hiện đang có 2 ổ đĩa: ổ đĩa mặc định của Cloud9 và ổ đĩa mặc định của NodeGroup.

![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.1.staticprovision.png?pc=60pt)

3. Nhấn **Create volume** để tạo ổ đĩa mới.
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.2.staticprovision.png?pc=60pt)

4. Thay thế thành **20** tại **Size (GiB)**.
5. Đặt **Availability Zone** là Availability Zone của Node. Trong trường hợp này là ap-southeast-1b.
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.3.staticprovision.png?pc=60pt)

6. Scroll down to the end of page and click on **Create volume**.
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.4.staticprovision.png?pc=60pt)

7. Chỉnh sửa **Name** của ổ đĩa EBS vừa tạo ```fcj-ebs-strorage-eks```.
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.5.staticprovision.png?pc=60pt)


8. Lưu lại **Volume ID** để sử dụng sau.
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.6.staticprovision.png?pc=60pt)


### Tạo các tệp manifest
1. Tại cửa sổ lệnh Cloud9, tạo một thư mục tên ```static-provision/kube-manifest```.
```
mkdir -p static-provision/kube-manifest
ls
ls static-provision
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.7.staticprovision.png?pc=60pt)

2. Tạo một tệp tên **pv.yaml** bên trong **static-provision/kube-manifest**.
```
touch static-provision/kube-manifest/pv.yaml
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.8.staticprovision.png?pc=60pt)

3. Mở tệp **pv.yaml**, dán đoạn mã code dưới đây.
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: test-pv
spec:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 10Gi
  csi:
    driver: ebs.csi.aws.com
    fsType: ext4
    volumeHandle: <REPLACE-WITH-YOUR-VOLUME-ID>
```

4.Thay thế với **Volume ID** của bạn tại **volumeHandle**. Sau đó lưu lại.
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.9.staticprovision.png?pc=60pt)

5. Tạo một tệp tên **pv-claim.yaml** bên trong **static-provision/kube-manifest**.
```
touch static-provision/kube-manifest/pv-claim.yaml
```

6. Mở tệp **pv-claim.yaml**, dán đoạn mã code dưới đây. Sau đó lưu lại.
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-claim
spec:
  storageClassName: "" # Empty string must be explicitly set otherwise default StorageClass will be set
  volumeName: test-pv
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.10.staticprovision.png?pc=60pt)

7. Tạo một tệp tên **pod.yaml** bên trong **static-provision/kube-manifest**.
``` 
touch static-provision/kube-manifest/pod.yaml
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.11.staticprovision.png?pc=60pt)

8. Mở tệp **pod.yaml**, dán đoạn mã code bên dưới. Sau đó lưu lại.
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
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.12.staticprovision.png?pc=60pt)

### Triển khai tài nguyên
1. Tại cửa sổ lệnh Cloud9, thực thi câu lệnh dưới đây.
```
kubectl apply -f static-provision/kube-manifest
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.13.staticprovision.png?pc=60pt)

### Kiểm tra kết quả
1. Liệt kê **Persistent Volume (PV)**.
```
kubectl get pv
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.15.staticprovision.png?pc=60pt)
Có một PV tên **test-pv** với dung lượng là **10GB**.

2. Liệt kê **Persistent Volume Claim (PVC)**.
```
kubectl get pvc
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.16.staticprovision.png?pc=60pt)
Có một PVC tên **ebs-claim**, trạng thái là **BOUND**, ổ đĩa là **test-pv** và dung lượng là **10GB**.

3. Liệt kê **Pod**.
```
kubectl get pod
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.17.staticprovision.png?pc=60pt)

Có một Pod tên **app** với trạng thái là **Running**.

4. Chúng ta sẽ xác minh Pod viết dữ liệu vào ổ đĩa được cung cấp tĩnh thành công.
```
kubectl exec app -- cat /data/out.txt
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.18.staticprovision.png?pc=60pt)

Có nhiều dữ liệu được viết vào tệp **/data/out.txt**. Hãy lưu lại nội dung của ba dữ liệu đầu tiên để so sánh sau.
Trong trường hợp này là **Mon May 6 17:15:04 UTC 2024**, **Mon May 6 17:15:09 UTC 2024** và **Mon May 6 17:15:14 UTC 2024**.


Bây giờ, chúng ta sẽ xóa Pod sau đó tạo lại một cái khác để kiểm tra dữ liệu trong ổ đĩa EBS đã được cố định với Pod mới.

5. Xóa Pod hiện tại.
```
kubectl delete pod app
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.19.staticprovision.png?pc=60pt)
6. Liệt kê tất cả các Pod lần nữa để đảm bảo Pod đã được xóa
```
kubectl get pod
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.20.staticprovision.png?pc=60pt)

7. Tạo một Pod mới.
```
kubectl apply -f static-provision/kube-manifest
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.21.staticprovision.png?pc=60pt)

8. Liệt kê Pod vừa tạo.
```
kubectl get pod
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.22.staticprovision.png?pc=60pt)

9. Xác minh dữ liệu trong **/data/out.txt** lần nữa.
```
kubectl exec app -- cat /data/out.txt
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.23.staticprovision.png?pc=60pt)

Bạn có thể thấy, ba dữ liệu đầu tiên trong **/data/out.txt** hiện vẫn là **Mon May 6 17:15:04 UTC 2024**, **Mon May 6 17:15:09 UTC 2024** và **Mon May 6 17:15:14 UTC 2024**. Dữ liệu được giữ nguyên trong khi Pod được thay thế bằng cái khác.

### Dọn dẹp
1. Thực thi câu lệnh dưới để xóa tất cả tài nguyến.
```
kubectl delete -f static-provision/kube-manifest
```
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.24.staticprovision.png?pc=60pt)

2. Xác minh rằng tất cả các tài nguyên đã được xóa thành công.
```
kubectl get pv,pvc,pod
```

![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.25.staticprovision.png?pc=60pt)

Tất cả đã được xóa.

3. Đi đến [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes:v=3) để xóa ổ đĩa tên ```fcj-ebs-strorage-eks```.
4. Chọn ổ đĩa tên ```fcj-ebs-strorage-eks```.
5. Nhấn **Actions**.
6. Nhấn **Delete volume**
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.26.staticprovision.png?pc=60pt)

7. Nhập ```delete``` để xác nhận.
8. Sau đó, nhấn **Delete**.
![Cung cấp tĩnh](../../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.27.staticprovision.png?pc=60pt)
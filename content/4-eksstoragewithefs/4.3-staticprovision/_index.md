---
title : "Static Provisioning"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---

### Store existing resources.
1. Create a dicrectory **EBS** to store all resources of **EBS** section.
```
mkdir EBS
```
2. Move all directories **dynamic-provision** and **static-provision** to **EBS** directory.
```
mv dynamic-provision EBS
mv static-provision EBS
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.1.staticprovision.png?pc=60pt)

### Create Manifest files.
1. Create a directory name ```EFS``` to store the kube-manifest files.
```
mkdir EFS
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.2.staticprovision.png?pc=60pt)

2. Create the directory for **Static Provisioning** section.
```
mkdir -p EFS/static-provision/kube-manifest
ls EFS/static-provision
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.3.staticprovision.png?pc=60pt)

3. Create file name **storageclass.yaml** inside **static-provision/kube-manifest**.
```
touch EFS/static-provision/kube-manifest/storageclass.yaml
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.4.staticprovision.png?pc=60pt)

4. Open **storageclass.yaml** file, paste the below code.
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: efs-sc
provisioner: efs.csi.aws.com
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.5.staticprovision.png?pc=60pt)

5. Create file name **pv.yaml** inside **static-provision/kube-manifest**.
```
touch EFS/static-provision/kube-manifest/pv.yaml
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.6.staticprovision.png?pc=60pt)

6. Open **pv.yaml** file, paste the below code. Input your EFS file system ID at **volumeHandle**.
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: efs-pv
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  storageClassName: efs-sc
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: efs.csi.aws.com
    volumeHandle: fs-0db5cdf65fc6c821d
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.7.staticprovision.png?pc=60pt)

7. Create file name **pvc.yaml** inside **static-provision/kube-manifest**.
```
touch EFS/static-provision/kube-manifest/pvc.yaml
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.8.staticprovision.png?pc=60pt)

8. Open **pvc.yaml** file, paste the below code.
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: efs-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: efs-sc
  resources:
    requests:
      storage: 5Gi
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.9.staticprovision.png?pc=60pt)

9. Create file name **pod.yaml** inside **static-provision/kube-manifest**.
```
touch EFS/static-provision/kube-manifest/pod.yaml
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.10.staticprovision.png?pc=60pt)

10. Open **pod.yaml** file, paste the below code.
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
    args: ["-c", "while true; do echo $(date -u) >> /data/out.txt; sleep 5; done"]
    volumeMounts:
    - name: persistent-storage
      mountPath: /data
  volumes:
  - name: persistent-storage
    persistentVolumeClaim:
      claimName: efs-claim
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.11.staticprovision.png?pc=60pt)

### Deploy resources
1. Execute below command to deploy the resources.
```
kubectl apply -f EFS/static-provision/kube-manifest
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.12.staticprovision.png?pc=60pt)

2. List all created resources.
```
kubectl get sc,pv,pvc,pod
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.13.staticprovision.png?pc=60pt)

{{% notice important %}}
**STATUS** of **Persistent Volume (pv)** and **Persistent Volume Claim (pvc)** must be **Bound**, and of **Pod** must be **Running**.
{{% /notice %}}

### Verify the result.
1. Execute below command to verify that data is written onto EFS filesystem.
```
kubectl exec -ti efs-app -- head /data/out.txt
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.14.staticprovision.png?pc=60pt)

There are a lot of data wrote to **/data/out.txt** file. Let take note the name of three first data to compare later.
In my case is **Wed May 8 11:31:17 UTC 2024**, **Wed May 8 11:31:22 UTC 2024** and **Wed May 8 11:31:27 UTC 2024**.

2. Now we will delete the current Pod. Then list all resources again to make sure that Pod was deleted.
```
kubectl delete pod efs-app
kubectl get sc,pv,pvc,pod
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.15.staticprovision.png?pc=60pt)

Pod **efs-app** was deleted.

3. Create a new pod.
```
kubectl apply -f EFS/static-provision/kube-manifest/pod.yaml
kubectl get pod
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.16.staticprovision.png?pc=60pt)
4. Verify the data on **/data/out.txt** again.
```
kubectl exec -ti efs-app -- head /data/out.txt
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.17.staticprovision.png?pc=60pt)


As you can see, the three first of data on **/data/out.txt** now are still **Wed May 8 11:31:17 UTC 2024**, **Wed May 8 11:31:22 UTC 2024** and **Wed May 8 11:31:27 UTC 2024**. The data is still kept while the pod is replaced with another one.

### Clean up
1. Execute below command to delete all created resources.
```
kubectl delete -f EFS/static-provision/kube-manifest
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.18.staticprovision.png?pc=60pt)

2. Verify that all resources had been deleted successfully.
```
kubectl get sc,pv,pvc,pod
```
![Static Provisioning](../../images/4.eksstoragewithefs/4.3.staticprovision/4.3.19.staticprovision.png?pc=60pt)

All of them are deleted.


---
title : "Dynamic Provisioning"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4.4 </b> "
---

### Create Manifest files
1. Create a directory for **Dynamic Provisioning** section.
```
mkdir -p EFS/dynamic-provision/kube-manifest
ls EFS/dynamic-provision
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.1.dynamicprovision.png?pc=60pt)

2. Create file name **storageclass.yaml** inside **dynamic-provision/kube-manifest**.
```
touch EFS/dynamic-provision/kube-manifest/storageclass.yaml
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.2.dynamicprovision.png?pc=60pt)

3. Open **storageclass.yaml** file, paste the below code. Replace with your **EFS File System ID** at **fileSystemId** field.
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
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.3.dynamicprovision.png?pc=60pt)

4. Create file name **pvc.yaml** inside **dynamic-provision/kube-manifest**.
```
touch EFS/dynamic-provision/kube-manifest/pvc.yaml
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.4.dynamicprovision.png?pc=90pt)

5. Open **pvc.yaml** file, paste the below code.
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
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.5.dynamicprovision.png?pc=60pt)

6. Create file name **pod.yaml** inside **dynamic-provision/kube-manifest**.
```
touch EFS/dynamic-provision/kube-manifest/pod.yaml
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.6.dynamicprovision.png?pc=60pt)

7. Open **pod.yaml** file, paste the below code.
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
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.7.dynamicprovision.png?pc=60pt)

### Deploy resources
1. Execute below command to deploy resources.
```
kubectl apply -f EFS/dynamic-provision/kube-manifest
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.8.dynamicprovision.png?pc=60pt)

2. List all created resources.
```
kubectl get sc,pvc,pod
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.9.dynamicprovision.png?pc=60pt)

{{% notice important %}}
**STATUS** of **Persistent Volume Claim (pvc)** must be **Bound**, and of **Pod** must be **Running**.
{{% /notice %}}

3. Go to [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems).
4. Click on your EFS File System.
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.10.dynamicprovision.png?pc=60pt)

5. Navigate to **Access points** tab.
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.11.dynamicprovision.png?pc=60pt)

You can see there is an **Access point** had been created since you specified **parameters.provisioningMode** is **efs-ap** on **storageclass.yaml** file.

### Verify the result.
1. Execute below command to verify that data is written onto EFS filesystem.
```
kubectl exec efs-app -- bash -c "cat data/out"
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.12.dynamicprovision.png?pc=60pt)

There are a lot of data wrote to **/data/out** file. Let take note the name of three first data to compare later.
In my case is **Wed May 8 17:52:02 UTC 2024**, **Wed May 8 17:52:07 UTC 2024** and **Wed May 8 17:52:12 UTC 2024**.

2. Now we will delete the current Pod. Then list all resources again to make sure that Pod was deleted.
```
kubectl delete pod efs-app
kubectl get sc,pvc,pod
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.13.dynamicprovision.png?pc=60pt)

Pod **efs-app** was deleted.

3. Create a new pod.
```
kubectl apply -f EFS/dynamic-provision/kube-manifest/pod.yaml
kubectl get pod
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.14.dynamicprovision.png?pc=60pt)

4. Verify the data on **/data/out** again.
```
kubectl exec efs-app -- bash -c "cat data/out"
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.15.dynamicprovision.png?pc=60pt)


As you can see, the three first of data on **/data/out** now are still **Wed May 8 17:52:02 UTC 2024**, **Wed May 8 17:52:07 UTC 2024** and **Wed May 8 17:52:12 UTC 2024**. The data is still kept while the pod is replaced with another one.

### Clean up
1. Execute below command to delete all created resources.
```
kubectl delete -f EFS/dynamic-provision/kube-manifest
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.16.dynamicprovision.png?pc=60pt)
2. Verify that all resources had been deleted successfully.
```
kubectl get sc,pvc,pod
```
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.17.dynamicprovision.png?pc=60pt)

3. Go to [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems).
4. Select your EFS File System.
5. Click on **Delete**.
![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.18.dynamicprovision.png?pc=60pt)

6. Input your EFS File System ID to confirm.
7. Click on **Confirm** to delete.

![Dynamic Provisioning](../../images/4.eksstoragewithefs/4.4.dynamicprovision/4.4.19.dynamicprovision.png?pc=60pt)
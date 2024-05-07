---
title : "Dynamic Provisioning"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---

### Create manifest files
1. At Cloud9 Terminal, create a directory name ```dynamic-provision/kube-manifest```.
```
mkdir -p dynamic-provision/kube-manifest
```
2. Create a file named **storageclass.yaml** inside **dynamic-provision/kube-manifest**.
```
touch dynamic-provision/kube-manifest/storageclass.yaml
```
3. Open **storageclass.yaml** file, paste the below code. Then save it.
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.1.dynamicprovision.png?pc=60pt)

4. Create a file named **pv-claim.yaml** inside **dynamic-provision/kube-manifest**.
```
touch dynamic-provision/kube-manifest/pv-claim.yaml
```
5. Open **pv-claim.yaml** file, paste the below code. Then save it.
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
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.2.dynamicprovision.png?pc=60pt)

6. Create a file named **pod.yaml** inside **dynamic-provision/kube-manifest**.
```
touch dynamic-provision/kube-manifest/pod.yaml
```

7. Open **pod.yaml** file, paste the below code. Then save it.
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
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.3.dynamicprovision.png?pc=60pt)

### Deploy resources### Deploy the resources
1. At Cloud9 Terminal, execute the below command.
```
kubectl apply -f dynamic-provision/kube-manifest
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.4.dynamicprovision.png?pc=60pt)

### Verify the result
1. List all created resources.
```
kubectl get sc,pvc,pod
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.5.dynamicprovision.png?pc=60pt)

There is a Pod named **app** with status is **Running**, a Persistent Volume Claim named **ebs-claim** with status is **Bound** and capacity is **10Gi** and a Storage Class named **ebs-sc** with Provider is **ebs.csi.aws.com**.
Storage Class will dynamically create an EBS Volume with volume size defined at **spec.resources.requests.storage** (10Gi)..

2. Go to [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes) to verify the result.
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.6.dynamicprovision.png?pc=60pt)

There is a new EBS Volume with Availability Zone same as Node's AZ.

3. We will validate the pod successfully wrote data to the dynamically provisioned volume.
```
kubectl exec app -- cat /data/out.txt
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.7.dynamicprovision.png?pc=60pt)

There are a lot of data wrote to **/data/out.txt** file. Let take note the name of three first data to compare later.
In my case is **Mon May 6 19:58:43 UTC 2024**, **Mon May 6 19:58:48 UTC 2024** and **Mon May 6 19:58:53 UTC 2024**.

Now, we will delete the pod then create another new pod to verify data in EBS volume is persistent with the new one.

4. Delete the current Pod **app**.
```
kubectl delete pod app
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.8.dynamicprovision.png?pc=60pt)
5. List all Pods again to make sure it was deleted.
```
kubectl get pod
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.9.dynamicprovision.png?pc=60pt)
6. Create a new Pod.
```
kubectl apply -f dynamic-provision/kube-manifest
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.10.dynamicprovision.png?pc=60pt)
7. List created Pod.
```
kubectl get pod
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.11.dynamicprovision.png?pc=60pt)
8. Verify the data on **/data/out.txt** again.
```
kubectl exec app -- cat /data/out.txt
```
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.12.dynamicprovision.png?pc=60pt)
As you can see, the three first of data on **/data/out.txt** now are still **Mon May 6 19:58:43 UTC 2024**, **Mon May 6 19:58:48 UTC 2024** and **Mon May 6 19:58:53 UTC 2024**. The data is still kept while the pod is replaced with another one.

### Clean up
1. Execute the below command to delete all created resources.
```
kubectl delete -f dynamic-provision/kube-manifest
```

![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.13.dynamicprovision.png?pc=60pt)

2. Verify that all resources had been deleted successfully.
```
kubectl get sc,pvc,pod
```

![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.14.dynamicprovision.png?pc=60pt)

All of them are deleted.
Because Amazon EBS Volume is created dynamically by Storage Class. So when Storage Class is deleted, Amazon EBS Volume is also removed.

3. Go to [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes) to verify the result.
![Dynamic Provision](../../images/3.eksstoragewithebs/3.3.dynamicprovision/3.3.15.dynamicprovision.png?pc=60pt)

Amazon EBS Volume was removed automatically.
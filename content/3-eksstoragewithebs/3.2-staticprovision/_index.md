---
title : "Static Provisioning"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

In this step, we will practice how to create and consume a PersistentVolume from an existing EBS volume with static provisioning.


### Verify the Node's Availability Zone
First, we need to check which Availability Zone that our EC2 Managed Node Group Instance is placing.
1. Go to [EC2 Instances](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Instances:tag:Name=fcj-storage-cluster-fcj-storage-nodegroup-Node;v=3;$case=tags:true%5C,client:false;$regex=tags:false%5C,client:false).
2. Verify its Availability Zone.
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.14.staticprovision.png?pc=60pt)

In my case the Availability Zone of Node is ap-southeast-1b, so now i will create an EBS Volume at ap-southeast-1b.

### Create an EBS volume
1. Go to [EBS Volumes](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes).
2. You will see there are two volumes: default volume of Cloud9 instance and default volume of NodeGroup instance.

![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.1.staticprovision.png?pc=60pt)

3. Click on **Create volume** to create new volume.
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.2.staticprovision.png?pc=60pt)

4. Replace to **20** at **Size (GiB)** field.
5. Set **Availability Zone** as Node's Availability Zone. In this case is ap-southeast-1b.
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.3.staticprovision.png?pc=60pt)

6. Scroll down to the end of page and click on **Create volume**.
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.4.staticprovision.png?pc=60pt)

7. Edit **Name** of created EBS Volume ```fcj-ebs-strorage-eks```.
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.5.staticprovision.png?pc=60pt)


8. Save the **Volume ID** to use later.
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.6.staticprovision.png?pc=60pt)


### Create manifest files
1. At Cloud9 Terminal, create a directory name ```static-provision/kube-manifest```.
```
mkdir -p static-provision/kube-manifest
ls
ls static-provision
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.7.staticprovision.png?pc=60pt)

2. Create a file named **pv.yaml** inside **static-provision/kube-manifest**.
```
touch static-provision/kube-manifest/pv.yaml
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.8.staticprovision.png?pc=60pt)

3. Open **pv.yaml** file, paste the below code.
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

4. Replace with your **Volume ID** at **volumeHandle**. Then save it.
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.9.staticprovision.png?pc=60pt)

5. Create a file named **pv-claim.yaml** inside **static-provision/kube-manifest**.
```
touch static-provision/kube-manifest/pv-claim.yaml
```

6. Open **pv-claim.yaml** file, paste the below code. Then save it.
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
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.10.staticprovision.png?pc=60pt)

7. Create a file named **pod.yaml** inside **static-provision/kube-manifest**.
``` 
touch static-provision/kube-manifest/pod.yaml
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.11.staticprovision.png?pc=60pt)

8. Open **pod.yaml** file, paste the below code. Then save it.
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
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.12.staticprovision.png?pc=60pt)

### Deploy the resources
1. At Cloud9 Terminal, execute the below command.
```
kubectl apply -f static-provision/kube-manifest
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.13.staticprovision.png?pc=60pt)

### Verify the result
1. List the **Persistent Volume (PV)**.
```
kubectl get pv
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.15.staticprovision.png?pc=60pt)
There is a PV named **test-pv** with capacity is **10GB**.

2. List the **Persistent Volume Claim (PVC)**.
```
kubectl get pvc
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.16.staticprovision.png?pc=60pt)
There is a PVC named **ebs-claim**, status is **BOUND**, volume is **test-pv** and capacity is **10GB**.

3. List the **Pod**.
```
kubectl get pod
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.17.staticprovision.png?pc=60pt)

There is a Pod named **app** with status is **Running**.

4. We will validate the pod successfully wrote data to the statically provisioned volume.
```
kubectl exec app -- cat /data/out.txt
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.18.staticprovision.png?pc=60pt)

There are a lot of data wrote to **/data/out.txt** file. Let take note the name of three first data to compare later.
In my case is **Mon May 6 17:15:04 UTC 2024**, **Mon May 6 17:15:09 UTC 2024** and **Mon May 6 17:15:14 UTC 2024**.

Now, we will delete the pod then create another new pod to verify data in EBS volume is persistent with the new one.

5. Delete the current Pod.
```
kubectl delete pod app
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.19.staticprovision.png?pc=60pt)
6. List all Pods again to make sure it was deleted.
```
kubectl get pod
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.20.staticprovision.png?pc=60pt)

7. Create a new Pod.
```
kubectl apply -f static-provision/kube-manifest
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.21.staticprovision.png?pc=60pt)

8. List created Pod.
```
kubectl get pod
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.22.staticprovision.png?pc=60pt)

9. Verify the data on **/data/out.txt** again.
```
kubectl exec app -- cat /data/out.txt
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.23.staticprovision.png?pc=60pt)

As you can see, the three first of data on **/data/out.txt** now are still **Mon May 6 17:15:04 UTC 2024**, **Mon May 6 17:15:09 UTC 2024** and **Mon May 6 17:15:14 UTC 2024**. The data is still kept while the pod is replaced with another one.

### Clean up
1. Execute the below command to delete all created resources.
```
kubectl delete -f static-provision/kube-manifest
```
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.24.staticprovision.png?pc=60pt)

2. Verify that all resources had been deleted successfully.
```
kubectl get pv,pvc,pod
```

![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.25.staticprovision.png?pc=60pt)

All of them are deleted.

3. Go to [EBS Volume](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Volumes:v=3) to delete the EBS Volume named ```fcj-ebs-strorage-eks```.
4. Select the volume named ```fcj-ebs-strorage-eks```.
5. Click on **Actions**.
6. Click on **Delete volume**
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.26.staticprovision.png?pc=60pt)

7. Input ```delete``` to confirm.
8. Then, click on **Delete**.
![Static Provision](../../images/3.eksstoragewithebs/3.2.staticprovision/3.2.27.staticprovision.png?pc=60pt)
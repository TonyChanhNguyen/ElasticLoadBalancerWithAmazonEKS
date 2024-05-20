---
title : "Use ExternalDNS as Ingress Service"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---


### Create Manifest files.
1. Create a new working directory.
```
mkdir ingress/externaldns/externaldns-ingress
ls ingress/externaldns
```

![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.1.externaldnsingress.png?pc=90pt)

2. Copy all resources on **ingress/context-based-routing-ingress** to **ingress/externaldns/externaldns-ingress**.
```
cp ingress/context-based-routing-ingress/* ingress/externaldns/externaldns-ingress
ls ingress/externaldns/externaldns-ingress
```

![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.2.externaldnsingress.png?pc=90pt)


3. Open file **05-ALB-ingress.yaml**, add the below annotation. Replace with your **DNS Name**.
```
# External DNS - For creating a Record Set in Route53
external-dns.alpha.kubernetes.io/hostname: <REPLACE-WITH-YOUR-DNSNAME>
```
![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.3.externaldnsingress.png?pc=90pt)

### Deploy resources.
1. Deploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/externaldns-ingress -n fcj-external-dns-ingress-ns
```
![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.4.externaldnsingress.png?pc=90pt)

2. Get all created resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl get all -n fcj-external-dns-ingress-ns
```
![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.5.externaldnsingress.png?pc=90pt)

3. Access to [Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1#) click to your **Host Zone** to verify there is a new DNS Record **firstcloudjourney** created.

![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.6.externaldnsingress.png?pc=90pt)

### Verify the result.
1. Access to ```http://<YOUR-DNS-NAME>/app1```.
![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.7.externaldnsingress.png?pc=90pt)

2. Access to ```http://<YOUR-DNS-NAME>/app2```.
![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.8.externaldnsingress.png?pc=90pt)

#### Congratulations, you had deployed your application with DNS Name successfully.

### Clean up.
1. Delete the created resources.
```
kubectl delete -f ingress/externaldns/externaldns-ingress -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.9.externaldnsingress.png?pc=90pt)

We will retain resources which related to **external-dns** service for next sections.

2. Go to [Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1#) click to your **Host Zone** to verify the created DNS Record **firstcloudjourney** deleted automatically when Ingress Service deleted.
![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.10.externaldnsingress.png?pc=90pt)


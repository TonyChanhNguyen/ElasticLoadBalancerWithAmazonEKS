---
title : "Name Based Virtual Host Routing"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 5.3 </b> "
---

In this section, we will implement Host Header routing using Ingress.

### Create Manifest files.
1. Create new working directory for this section.
```
mkdir ingress/externaldns/host-header-routing-ingress
ls ingress/externaldns
```
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.1.namebasedhostroutingingress.png?pc=90pt)

2. We will re-use the resources of previous section. Let copy all resources inside **ingress/externaldns/externaldns-ingress** to **ingress/externaldns/host-header-routing-ingress**.
```
cp ingress/externaldns/externaldns-ingress/* ingress/externaldns/host-header-routing-ingress
ls ingress/externaldns/host-header-routing-ingress
```
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.2.namebasedhostroutingingress.png?pc=90pt)

3. Open file **05-ALB-ingress.yaml**, delete all its definitions and replace with the below code. Then, replace ```<REPLACE-WITH-YOUR-HOST-HEADER-APP1>``` and ```<REPLACE-WITH-YOUR-HOST-HEADER-APP2>``` with your **Host Header** of each application.
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

![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.3.namebasedhostroutingingress.png?pc=90pt)

### Deploy resources.
1. Deploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/host-header-routing-ingress -n fcj-external-dns-ingress-ns
```
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.4.namebasedhostroutingingress.png?pc=90pt)

2. List all created resources.
```
kubectl get all -n fcj-external-dns-ingress-ns
```
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.5.namebasedhostroutingingress.png?pc=90pt)

3. Access to [Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1#) click to your **Host Zone** to verify there are two new DNS Records **firstcloudjourney-app1** and **firstcloudjourney-app2** created.
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.6.namebasedhostroutingingress.png?pc=90pt)

### Verify the result.
1. Access to ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.7.namebasedhostroutingingress.png?pc=90pt)

2. When you access to ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app2```. The returned result will be fail.
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.8.namebasedhostroutingingress.png?pc=90pt)

3. Access to ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2```.
![Use ExternalDNS as Ingress Service](../../images/5.externaldns/5.2.externaldnsingress/5.2.9.externaldnsingress.png?pc=90pt)

4. When you access to ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app1```. The returned result will be fail.
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.10.namebasedhostroutingingress.png?pc=90pt)

#### Congratulations, you had deployed your application with DNS Name Based Virtual Host Routing successfully.

### Clean up.
1. Delete the created resources.
```
kubectl delete -f ingress/externaldns/host-header-routing-ingress -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.11.namebasedhostroutingingress.png?pc=90pt)

We will retain resources which related to **external-dns** service for next sections.

2. Go to [Route53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1#) click to your **Host Zone** to verify the created DNS Records **firstcloudjourney-app1** and **firstcloudjourney-app2** deleted automatically when Ingress Service deleted.
![Name Based Virtual Host Routing](../../images/5.externaldns/5.3.namebasedhostroutingingress/5.3.12.namebasedhostroutingingress.png?pc=90pt)
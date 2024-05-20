---
title : "SSL and SSL Redirect"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 5.4 </b> "
---

In this section, we will create a SSL certificate for application. Then, redirect all HTTP traffics to HTTPS port.

## Integrate SSL certificate to your application.

#### Create a SSL Certificate in Certificate Manager
1. Go to [AWS Certificate Manager](https://ap-southeast-1.console.aws.amazon.com/acm/home?region=ap-southeast-1#/welcome). Click on **Request a certificate**.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.2.sslandsslredirect.png?pc=90pt)

2. At **Request certificate**, click on **Next**.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.3.sslandsslredirect.png?pc=90pt)

3. Input domain name with format ```*.<YOUR-DOMAIN-NAME>``` at **Fully qualified domain name** field.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.4.sslandsslredirect.png?pc=90pt)

4. Keep default at another fields. Then click on **Request**.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.5.sslandsslredirect.png?pc=90pt)

5. Your **Certificate** is created but in **Pending validation** status.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.6.sslandsslredirect.png?pc=90pt)

6. Let create a new record in Route53 for validation. Click on **Create records in Route 53**.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.7.sslandsslredirect.png?pc=90pt)

7. Click on **Create records**.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.8.sslandsslredirect.png?pc=90pt)

8. Go to [Route 53](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=ap-southeast-1), click on your **Host Zone** to see there is a new record created by **AWS Certificate Manager**.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.9.sslandsslredirect.png?pc=90pt)

9. Back to [AWS Certificate Manager](https://ap-southeast-1.console.aws.amazon.com/acm/home?region=ap-southeast-1#/certificates/list) to see the status your created Certificate changed to **Issued**.  
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.10.sslandsslredirect.png?pc=90pt)

{{% notice info %}}
It will take about 10 minutes to validate successful.
{{% /notice %}}

10. Save the ARN to use later.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.11.sslandsslredirect.png?pc=90pt)

#### Create Manifest files.
1. Create new working directory for this section.
```
mkdir ingress/externaldns/ssl-and-ssl-redirect
ls ingress/externaldns
```
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.1.sslandsslredirect.png?pc=90pt)

2. We will re-use the resources of previous section. Let copy all resources inside **ingress/externaldns/host-header-routing-ingress** to **ingress/externaldns/ssl-and-ssl-redirect**.
```
cp ingress/externaldns/host-header-routing-ingress/* ingress/externaldns/ssl-and-ssl-redirect
ls ingress/externaldns/ssl-and-ssl-redirect
```
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.12.sslandsslredirect.png?pc=90pt)

3. Open file **05-ALB-ingress.yaml**, add the below annotations. Replace ```<REPLACE-WITH-YOUR-CERTIFICATE-ARN>``` with your created Certificate Arn and save it.

```
## SSL Settings
alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS":443}, {"HTTP":80}]'
alb.ingress.kubernetes.io/certificate-arn: <REPLACE-WITH-YOUR-CERTIFICATE-ARN>
```
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.13.sslandsslredirect.png?pc=90pt)

#### Deploy resources
1. Deploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/ssl-and-ssl-redirect -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.14.sslandsslredirect.png?pc=90pt)

2. Go to [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:) to verify the new created Application Load Balancer.

![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.15.sslandsslredirect.png?pc=90pt)

3. Click on it and verify that there is a new added **Listener** for **HTTPS** protocol on Port **443**, and **Default SSL/TLS certificate** is the certificate which you created above.

![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.16.sslandsslredirect.png?pc=90pt)

4. Click on it to verify **Listener rules**. 
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.17.sslandsslredirect.png?pc=90pt)

+ With HTTPS traffic to port 443 with **HTTP Host Header** is ```firstcloudjourney-app1.<YOUR-DOMAIN-NAME>```, it will be forwarded to **Target Group** of App1. 
+ With HTTPS traffic to port 443 with **HTTP Host Header** is ```firstcloudjourney-app2.<YOUR-DOMAIN-NAME>```, it will be forwarded to **Target Group** of App2. 

### Verify the result.
1. Access to ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.18.sslandsslredirect.png?pc=90pt)

We can connect to the application App1 but the connection is not secure.

2. Access to ```https://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.19.sslandsslredirect.png?pc=90pt)

With HTTPS protocol, the connection to application App1 is secure.

3. Similarly, access to ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2``` and ```https://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2``` to verify the result.

![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.20.sslandsslredirect.png?pc=90pt)

![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.21.sslandsslredirect.png?pc=90pt)

## Redirect HTTP traffic to HTTPS Port.
#### Modify Manifest file.
1. Open file **05-ALB-ingress.yaml**, add the below annotations. Then save it.
```
# SSL Redirect Setting
alb.ingress.kubernetes.io/ssl-redirect: '443'   
```
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.22.sslandsslredirect.png?pc=90pt)

#### Redeploy resources.
1. Redeploy resources on Namespace **fcj-external-dns-ingress-ns**.
```
kubectl apply -f ingress/externaldns/ssl-and-ssl-redirect -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
Only the Ingress is configured.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.23.sslandsslredirect.png?pc=90pt)

2. Access to your Application Load Balancer on [Load Balancer](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#LoadBalancers:) again to see the changes.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.24.sslandsslredirect.png?pc=90pt)

The change appeared on **HTTP:80**, the **Rules** decreased from **3** to **1**.

3. Click on **HTTP:80** to verify the change.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.25.sslandsslredirect.png?pc=90pt)

All **Listener Rules** were deleted, there is a new one created which will redirect to **Port 443** when have traffic as default.


#### Verify the result.
1. Access to ```http://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.26.sslandsslredirect.png?pc=90pt)

The traffic will be redirected to **Port 443** with **HTTPS protocol** automatically.

2. Access to ```https://firstcloudjourney-app1.<YOUR-DNS-NAME>/app1```.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.27.sslandsslredirect.png?pc=90pt)

The traffic to **Port 443** with **HTTPS protocol** can still be accessed as normal.

3. Similarly, access to ```http://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2``` and ```https://firstcloudjourney-app2.<YOUR-DNS-NAME>/app2``` to verify the result.
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.28.sslandsslredirect.png?pc=90pt)

#### Clean up.
1. Delete the created resources.
```
kubectl delete -f ingress/externaldns/ssl-and-ssl-redirect -n fcj-external-dns-ingress-ns
kubectl get all -n fcj-external-dns-ingress-ns
```
![SSL and SSL Redirect](../../images/5.externaldns/5.4.sslandsslredirect/5.4.29.sslandsslredirect.png?pc=90pt)

We will retain resources which related to **external-dns** service and created **SSL certificate** on **AWS Certificate Manager** for next sections.
---
title : "Ingress service with Amazon EKS Cluster"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

### Overview
When you create a Kubernetes ingress, an AWS Application Load Balancer (ALB) is provisioned that load balances application traffic, ALBs can be used with Pods that are deployed to nodes or to AWS Fargate. You can deploy an ALB to public or private subnets.
![Deploy Load Balancer Service on Amazon EKS](../images/4.ingresswitheks/eksalb.png?pc=60pt)



### Content

+ 4.1 [Install AWS Load Balancer Controller](../4-ingresswitheks/4.1-installlbc/)
+ 4.2 [Create Basic Ingress](../4-ingresswitheks/4.2-basciingress/)
+ 4.3 [Context Path Based Routing Ingress](../4-ingresswitheks/4.3-conextbasedroutingingress/)
+ 4.4 [Context Path Based Routing Ingress on Fargate Profile](../4-ingresswitheks/4.4-ingresswithfargate/)
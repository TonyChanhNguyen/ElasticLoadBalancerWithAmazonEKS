---
title : "Deploy Load Balancer Service on Amazon EKS"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Deploy Load Balancer Service on Amazon EKS

### Overview

This workshop will provide a high level overview on how to integrate Load Balancer Service for application that run on Amazon EKS Cluster with Class Load Balancer, Application Load Balancer (Ingress Service) and Network Load Balancer.

Additionally, we will integrate Load Balancer Service with Domain Name System (DNS) Service via Route 53 and secure your traffic by AWS Certificate Manager.

![Deploy Load Balancer Service on Amazon EKS](images/eksingress.png?pc=60pt)

### Content

1. [Introduction](1-introduce/)
2. [Prerequisites](2-prerequiste/)
3. [Classic Load Balancer service with Amazon EKS Cluster](3-clbnlbwitheks/)
4. [Ingress service with Amazon EKS Cluster](4-ingresswitheks/)
5. [(Optional) Integrate ExternalDNS Service for Amazon EKS Cluster](5-dnsingresswitheks/)
6. [Network Load Balancer service with Amazon EKS Cluster](6-nlbwitheks/)
7. [Cleanup resources](7-cleanup/)
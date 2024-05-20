---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
### Overview

[Classic Load Balancer](https://aws.amazon.com/elasticloadbalancing/classic-load-balancer/) provides basic load balancing across multiple Amazon EC2 instances and operates at both the request level and connection level. Classic Load Balancer is intended for applications that are built within the EC2-Classic network. 


[Application Load Balancer](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/) operates at the request level (layer 7), routing traffic to targets (EC2 instances, containers, IP addresses, and Lambda functions) based on the content of the request. Ideal for advanced load balancing of HTTP and HTTPS traffic, Application Load Balancer provides advanced request routing targeted at delivery of modern application architectures, including microservices and container-based applications. Application Load Balancer simplifies and improves the security of your application, by ensuring that the latest SSL/TLS ciphers and protocols are used at all times.

[Network Load Balancer](https://aws.amazon.com/elasticloadbalancing/network-load-balancer/) operates at the connection level (Layer 4), routing connections to targets (Amazon EC2 instances, microservices, and containers) within Amazon VPC, based on IP protocol data. Ideal for load balancing of both TCP and UDP traffic, Network Load Balancer is capable of handling millions of requests per second while maintaining ultra-low latencies. Network Load Balancer is optimized to handle sudden and volatile traffic patterns while using a single static IP address per Availability Zone. It is integrated with other popular AWS services such as Auto Scaling, Amazon EC2 Container Service (ECS), Amazon CloudFormation, and AWS Certificate Manager (ACM).

[Route 53](https://aws.amazon.com/route53/) is a highly available and scalable Domain Name System (DNS) web service. Route 53 connects user requests to internet applications running on AWS or on-premises.

[AWS Certificate Manager](https://aws.amazon.com/certificate-manager/) helps to provision, manage, and deploy public and private SSL/TLS certificates for use with AWS services and your internal connected resources. ACM removes the time-consuming manual process of purchasing, uploading, and renewing SSL/TLS certificates.


![Deploy Load Balancer Service on Amazon EKS](../images/eksingress.png?pc=60pt)


### Content

1. [Introduction](../1-introduce/)
2. [Prerequisites](../2-prerequiste/)
3. [Classic Load Balancer service with Amazon EKS Cluster](../3-clbnlbwitheks/)
4. [Ingress service with Amazon EKS Cluster](../4-ingresswitheks/)
5. [(Optional) Integrate ExternalDNS Service for Amazon EKS Cluster](../5-dnsingresswitheks/)
6. [Network Load Balancer service with Amazon EKS Cluster](../6-nlbwitheks/)
7. [Cleanup resources](../7-cleanup/)
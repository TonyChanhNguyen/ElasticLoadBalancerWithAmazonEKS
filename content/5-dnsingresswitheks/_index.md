---
title : "(Optional) Integrate ExternalDNS Service for Amazon EKS Cluster"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
### Overview
In this chapter, we will demonstrate how to integrate ExternalDNS Service (Route53 and AWS Certificate Manager) on Amazon EKS Cluster.
This is an optional chapter, since it requests you need to have a associated Host Zone on Route53. If you do still not have it, you can purchase a domain name and add it on Host Zone or ony need to go through to get the overview of this concept.

![Deploy Load Balancer Service on Amazon EKS](../images/5.externaldns/eksalbdnsacm.png?pc=60pt)

### Content
+ 5.1 [Deploy ExternalDNS Service](../5-dnsingresswitheks/5.1-deployexternaldns/)
+ 5.2 [Use ExternalDNS as Ingress Service](../5-dnsingresswitheks/5.2-useexternaldnsasingress/)
+ 5.3 [Name Based Virtual Host Routing](../5-dnsingresswitheks/5.3-namebasedvirtualhostrouting/)
+ 5.4 [SSL and SSL Redirect](../5-dnsingresswitheks/5.4-sslandssldirect/)
+ 5.5 [Target Type IP](../5-dnsingresswitheks/5.5-targettypeip/)
---
title: The BattleBox
type: page
description: Turning a humble Pi4 into the Ultimate Platform Testbed
topic: career
---

### The goal of this project

With some time available at the end of my internship, I wanted to turn my Raspberry Pi 4B into a BattleBox - to tinker around with some very cool platform-related technologies that I wanted to get more familiar with.  

We'll have three main areas to cover: (1) Observability, (2) Security and (3) Networking.  

### Observability

{{< figure src="/images/obs-stack.png" title="" >}}

Our goal in this phase is to set up a working TIG stack on our RPI. TIG stands for Telegraf-InfluxDB-Grafana. This is a very common stack used for observing metrics. We use the RPI as a proxy for a bare metal server (because I can't afford to buy a whole rack). Assuming we load an application on the RPI (watch out for phase 2!), we want to see how much load our Raspberry Pi is taking on. This means looking at metrics such as CPU utilisation. It is useful to have a record of the data over time - for anomaly detection, forecasting or other reasons.  

Telegraf is used to extract this data from our RPI. Typically, we would run Linux commands such as `top` or `iostat` to obtain this data. But Telegraf can help log this data regularly and push it somewhere. We want to push this to InfluxDB, an open-source Time Series Database. Time Series Databases are very useful for efficient querying of time series data. We load this data into Grafana, an open-source visualisation tool, where we can see the metrics.  

### Security

{{< figure src="/images/SCIS.png" title="" >}}

Static Container Image Scanning or SCIS is a technique used to scan container images for known vulnerabilities. It forms a critical component of any DevSecOps pipeline, especially one that is focused on the management of container images. In this scenario, we want to load only scanned container images onto our RPI to prevent malicious activity such as credential leakage or remote code execution.  

We set up a CI/CD pipeline in AWS with CodePipeline. We integrate Trivy, a SCIS tool that helps us to scan our container image for vulnerabilities. Once cleared, we push this image into ECR, where it can be pulled by our RPI.  

While popular container images such as Samba can most likely be trusted (due to rigorous security checks), it would be useful to have our own security vulnerability detection pipeline to 'shift left' some of the risk before deployment.  

### Networking

{{< figure src="/images/multicastvpn.png" title="" >}}

The objective here is simple: we want to set up a Site to Site VPN from our RPI (with StrongSwan installed) and an AWS Transit Gateway. We then want to set up multicast-enabled ECS worker nodes to test multicasting from our TGW.  


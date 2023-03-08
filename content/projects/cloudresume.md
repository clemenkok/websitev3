---
title: Cloud Resume Challenge
dateMonthYear: Dec 2022
description:  Built a resume website using several AWS services - S3, Route53, CloudFront, SAM, DynamoDB, Lambda, API Gateway.
type: page
topic: project
image: "/images/aws.png"
---

{{< figure src="/images/cloudresume.png" title="" >}}

The objective of this project is to build a resume website using several AWS services. It is based off of the challenge created by Forrest Brazeal.

The challenge can be broken down into multiple stages.

> Stage 1: Cloud Website
> Skills: HTML/CSS, Git, cloud storage, CDN, DNS
> 
> Stage 2: Serverless API
> Skills: Cloud compute, NoSQL database, Python
> 
> Stage 3: Front End / Back End Integration
> Skills: Javascript, cloud monitoring, end-to-end testing
> 
> Stage 4: Infrastructure as Code and CI/CD
> Skills: Terraform, GitHub Actions, cloud secrets management  

Using this challenge as a guide, I created my own [website](http://clemenkok.com).  

Some key things that I learnt while building this project:

When using Amazon-generated SSL Certificates, infrastructure must be deployed on us-east-1 (N. Virginia) - this is an inherent limitation of Amazon-generated SSL Certificates. As I had initially used another zone, I had to restart my project on us-east-1.

I used the AWS SAM CLI for this project, allowing me to deploy my Infrasturucture as Code. This was extremely useful and efficient and I no longer had to click around the console. AWS SAM is a wrapper around CloudFormation, AWS' IaC service.

CI/CD. Stages: (1) Testing Infrastructure (functions that you have written to get/put data in DynamoDB), (2) On successful test build and deploy infrastructure, (3) afterwards deploy the site (changes to site i.e. website code).

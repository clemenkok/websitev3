---
title: Should your Lambda be in a Private Subnet?
type: page
description: A primer on Lambda Networking and Security
topic: career
---

### Introduction

"In a serverless architecture, you should put the Lambda functions in a private subnet in order to harden the network."  

How true is this statement? To break it down, let's dive deep into Lambda networking internals.  

{{< figure src="/images/lambdaprivate.png" title="" >}}

### What is a VPC?

The Amazon VPC is a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you can define. In a VPC, we control networking configuration can provide or restrict access between resources inside/outside a VPC and we can connect to other netwroks via a VPN or via AWS Direct Connect to physical networks.  

{{< figure src="/images/vpc.png" title="" >}}

### Lambda Networking

How does Lambda work internally? Lambda functions run on compute resources managed by the Lambda service inside the Lambda service VPC. All invocations go through the service API, which is available via the public internet or via a VPC endpoint. There is no inbound networking to the function environment (important!).    

By default, when you create a Lambda function, it has access to the entire internet and any AWS API. But it does not have access to private resources (i.e. inside a VPC).  

{{< figure src="/images/vpclambda.png" title="" >}}

According to AWS, if you want to access resources inside another VPC, you configure it to be VPC-enabled. This creates a VPC to VPC NAT and an ENI inside your VPC. You also only do this if your function needs access to VPC-based resources (see table below). This is much more secure than the alternative, which is to expose the VPC resources to be publicly available, thus making traffic route through the public Internet to access VPC resources.  

Even then, Lambda functions always run in VPCs owned by the Lambda service team (and so, not your own VPC).  

The full list of services that require VPC configuration is below:  

|AWS services available by default|AWS services requiring VPC configuration |
|---|---|
| Amazon API Gateway | Amazon ECS  |
| Amazon CloudFront | Amazon EFS  |
| Amazon CloudWatch | Amazon ElastiCache  |
| Amazon Comprehend | Amazon OpenSearch Service  |
| Amazon DynamoDB | Amazon MSK  |
| Amazon EventBridge | Amazon MQ  |
| Amazon Kinesis | Amazon RDS  |
| Amazon Lex | Amazon Redshift  |
| Amazon Pinpoint |    |
| Amazon Polly |    |
| Amazon Rekognition |    |
| Amazon S3 |    |
| Amazon SNS |    |
| Amazon SQS |    |
| AWS Step Functions |    |
| Amazon Textract |    |
| Amazon Transcribe |    |
| Amazon Translate |    |

#### Hyperplane ENIs

Importantly, we also note that Lambda now also uses Hyperplane ENIs, which are a managed network resource that allows multiple execution environments to securely access resources inside of VPCs. This is much more scalable than before.  

In the past, more network interfaces would be created and attached to our Lambda infrastructure (see below). This causes more overhead in terms of managing the IP address space in our subnets and monitoring the account level network interface limit. It also causes longer cold-starts, increasing the time needed to spin up a new execution environment before our code can be invoked.  

{{< figure src="/images/oldlambda.png" title="" >}}

Now, Hyperplane creates a network interface when our Lambda function is created (90s~ initial setup). When a function is invoked, the execution environment uses the pre-created network interface, reducing latency. Hyperplane ENIs also shared across execution environments, cross-account.  

{{< figure src="/images/hypeni.png" title="" >}}

Cold start times were drastically reduced - dropping from 14.8s to 933ms, as per AWS' blog.  

#### So can you create a Lambda function inside your VPC?

No. What actually happens is that at function creation, you configure the Lambda to be VPC-enabled. As I stated earlier, Lambdas always run inside the service VPC (managed by the Lambda Team), and use ENIs to interface with your VPC-based resources.   

{{< figure src="/images/CONFIG.png" title="" >}}

It's behaviour is not changed (you still invoke it via the invoke action, which is access controlled by IAM). The only thing is that you remove outbound internet access when doing so, so you would need to include a NAT Gateway and Internet Gateway in your VPC.  

{{< figure src="/images/pubint.png" title="" >}}

### Is it secure if it's not in a VPC?

Lambda functions are protected by IAM, which provides both authentication and authroization. All incoming requests must be signed with a valid AWS credential, and the caller must have the correct IAM permission.  

Under the hood, we know that the EC2 instances that run our Lambda functions are not publicly accessible (they run inside the Lambda Service VPC and are only invokable via the invoke API).  

#### Egress Traffic

However, it is still possible to compromise Lambda functions. IAM stops malicious ingress traffic, but malicious egress traffic is still possible. Malicious dependencies can, for example, [steal sensitive data such as AWS credentials](https://www.youtube.com/watch?v=jUhiPj6h_L8&t=789s). One way to mitigate this is through the [FunctionShield](https://github.com/puresec/FunctionShield) library, which blocks outbound internet connectivity except those bound for other AWS resources.  

#### Stringent Security Requirements

However, mitigation may be insufficient, as certain use cases may have strict security control requirements.  

One way to overcome this is to implement egress control via AWS Network Firewall. Flow Logs can also be enabled for post-event analysis.  

{{< figure src="/images/nwfw.png" title="" >}}

However, this approach will incur additional cost and complexity.  

### Conclusion

Lambdas do not run in your private subnet. Lambdas run in the service VPC managed by AWS. If functions are not VPC-enabled, they may not be fully secure because egress traffic is still enabled by default. However, we can still mitigate this by using libraries such as [FunctionShield](https://github.com/puresec/FunctionShield). Egress control can be implemented via AWS Network Firewall. We only configure Lambdas to be VPC-enabled if they require access to VPC-based resources, as per the official AWS Guidance.   

At the end of the day, system design is a balance of trade-offs. A more secure architecure with egress controls incurs trade offs such as cost (money) and complexity (time, overhead).  

Other lens to focus on instead to implement defence in depth could include other security controls such as authentication and authorisation, access control, monitoring and logging and the use of web application firewalls.  

### References

- [re:Invent 2022: Architecting Secure Serverless Applications](https://www.youtube.com/watch?v=A8iHQjHv8nY)
- [re:Invent 2021: Serverless Security Best Practices](https://www.youtube.com/watch?v=nEaAuX4O9TU)
- [Lambda Security Whitepaper](https://aws.amazon.com/lambda/security-overview-of-aws-lambda)
- [Lumigo Blog Post on Lambdas and VPCs](https://lumigo.io/aws-lambda-deployment/lambda-vpc/)
- [Improved Networking for AWS Lambda Functions](https://aws.amazon.com/blogs/compute/announcing-improved-vpc-networking-for-aws-lambda-functions/)

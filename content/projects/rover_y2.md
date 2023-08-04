---
title: BalanceBug
dateMonthYear: Jun 2023
description:  Y2 Design Project
type: page
topic: project
---

{{< figure src="/images/aws.png" title="" >}}

{{< figure src="/images/archilinkedin.png" title="" >}}

![]("/images/bb.gif")

Developed self-balancing maze solver. Played role of full-stack software engineer for team project.  

Key contributions:  

- Utilised a Real-Time Operating System (RTOS) to coordinate concurrency and manage shared memory on the dual-core
ESP32 microcontroller. Supported integration of sensor fusion, dead reckoning, drift correction and trilateration for
autonomous navigation.
- Built the backend infrastructure using AWS, MySQL, Node.js, Sequelize and Express.js with real-time communication
using Mosquittto MQTT. Developed frontend user interface using React to control robot and visualise the path. Built
RESTful APIs to coordinate communication between frontend and backend.
- Leveraged Docker Compose for configuration management and Terraform to manage cloud resources, enabling easy
deployment and scaling of the system on cloud platforms. Established a reverse proxy with NGiNX and set up a CI/CD
Pipeline using GitHub Actions to automate test and deployment into EC2 Instance.  

Something I was quite proud of was picking up RTOS purely from documentation. When I was first learning Software Engineering I remember being stuck in tutorial hell for a while. Here, I read the docs and then went to implement our solution. Granted it lead to some bugs in production, but independently discovering how to fix them made this a much better learning experience.  

Nevertheless, I do admit that there was some things I would have done differently. The cloud architecture above is not secure at all. We allow 0.0.0.0 in (VPC ACL) and all software is deployed in a public subnet (likewise for the EC2 NACL, we have 0.0.0.0 ingress). This is totally insecure. I would have deployed the EC2 instances in private subnets with an intermediary such as a Bastion Host to route traffic into our EC2 instances. Oh well, we were literally coding to the last minute anyway.  
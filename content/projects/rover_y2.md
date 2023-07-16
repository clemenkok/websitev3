---
title: BalanceBug
dateMonthYear: Jun 2023
description:  Y2 Design Project
type: page
topic: project
---

{{< figure src="/images/aws.png" title="" >}}

{{< figure src="/images/y2rover.png" title="" >}}

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


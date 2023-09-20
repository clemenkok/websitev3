---
title: Securin' and Scalin' the Fediverse
type: page
description: Personal Docs
topic: career
---

### The goal of this project

With a humble Raspberry Pi 4, let us achieve the following objectives:

- Deploy an observability stack (Telegraf - InfluxDB - Grafana) on the Raspberry Pi. Export the grafana board and host it on a Web Server (NGiNX) so that it can be viewed from anywhere. Deploy a VPN service (Cloudflare Tunnels) to filter network ingress to our web server.
- Deploy a Mastodon server onto the Raspberry Pi. Perform security testing beforehand by setting up a DevSecOps pipeline in the AWS cloud and pulling the image from ECR onto the Raspberry Pi.

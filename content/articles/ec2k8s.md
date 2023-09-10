---
title: k8s on EC2 with KubeAdm
type: page
description: Personal Docs
topic: career
---

### What this guide is for 

To document my k8s homelabbing. End result is one master and one slave node with 2x EC2 instances.  

### Installation

- Spin up 2 x EC2 instances on the AWS Console. Initially used `t2.medium` but this is no good for development. Changed to t2.large for control plane node. Use Ubuntu 20.04 LTS.   
- Instructions below apply for both nodes.  
- Install container runtime on both nodes (Docker for me). Follow instructions [here](https://docs.docker.com/engine/install/ubuntu/).  
- Install container runtime interface. Used `cri-dockerd`. Follow instructions [here](https://github.com/Mirantis/cri-dockerd).  
- Install `kubeadm, kubectl and kubelet`. Instructions [here](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/).  
- Enable ports 6443 and 10250 inbound for EC2 security groups. 6443 is the Kubernetes API Server port and 10250 is the Kubelet API port. Not too sure if this is best practice, but this is a non-production environment.  
- On Control plane node / master node, run `sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --cri-socket=unix:///var/run/cri-dockerd.sock`. Subsequently follow instructions. Enter the `join` command inside the output text on the master node on the worker node.  
- Install a container networking interface such as Calico. Instructions [here](https://docs.tigera.io/calico/latest/getting-started/kubernetes/self-managed-onprem/). See the manifests page and use that.  

### Running workloads

- If you deploy a `NGiNX` image to one of the pods it should work. Subsequently run `kubectl get pods` to see if it is in a `READY` state.  
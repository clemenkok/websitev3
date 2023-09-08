---
title: Leetcode
type: page
description: SREs don't do leetcode
topic: career
---

### Introduction

I'm not someone who finds solving algorithmic puzzles enjoyable.  

I know my interests - infrastructure and systems engineering. Set up a k8s cluster using KinD and try to set up a GitOps pipeline with ArgoCD? Trawl through pages of EKS documentation to understand how it works? Read the Linux manual, mess around with BPF? Setting up UIs with React, even? That's the kind of stuff I'd rather be doing (in my free time).    

I've had a problem with Leetcode. It felt like a mental block - I couldn't get the engine up to leetcode regularly and do algorithmic puzzles because it simply didn't interest me. I also didn't find it enjoyable. Anyways, what does Leetcode have to do with solving problems on the job, especially if you want to be a AWS developer / infrastructure engineer?  

### Where Leetcode comes in

I think the key turning point for me was (1) encountering algorithms in SRE work and (2) actually completing a few coding challenges. The first recall of this would be when I was watching Kelsey Hightower's talk on Kubernetes - [Kubernetes for Sysadmins](https://www.youtube.com/watch?v=HlAXp0-M6SY). At one point in this talk, he explains how k8s uses the [bin packing algorithm](https://en.wikipedia.org/wiki/Bin_packing_problem) to solve resource allocation problems (with Tetris as an analogy). In k8s, the orchestrator needs to carefully allocate resources (CPU and memory) to maximise the number of workloads (pods) that can run on a node while mimimising resource wastage. Bin packing is used to optimise the way the orchestrator allocates resources.  

{{< figure src="/images/binpack.png" title="" >}}

That moment connected the dots between Leetcode (algorithmic puzzles) and SRE work. It was where I realised that algorithms do have a place in infrastructure; and if we want to know how our infrastructure works (and therefore, how to best optimise it), we need to understand algorithms. I began to encounter more and more examples of this - LRU cache algorithm, Optimal Routing (Djikstra), Leaky Bucket algorithm, Ring buffers in networking. When I read Designing Data Intensive Applications, I was surprised to see so much coverage on Algorithms and Data Structures in Database design - it felt as though my software systems course was super inadequate (inspiring me to take a focused, advanced databases course in Year 3).  

In a DBMS, balanced trees (BTrees) are used to create indexes in database tables. This enables fast location of data during search operations with logarithmic time, such as when you do a `SELECT` statement in SQL. This happens because BTrees are balanced, which means that the depth of the tree is relatively constant as data is inserted and removed (the tree height is logarithmic in relation to the number of elements, causing the worse-case lookup time to be `O(logn)`).  

I can find numerous other examples across Operating Systems to Networking and other typical infra-related topics where Algorithms and Data Structures can be found. So if you want to know how a system works, and more importantly, how it is optimised, it eventually boils down to algorithms and data structures.  

### Coding Assessments

So 
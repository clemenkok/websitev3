---
title: Docker Compose for Node JS
type: page
description: Using docker-compose in NodeJS
topic: career
---

[Original GitHub Link](https://github.com/clemenkok/DockerComposeforNodeJS)

# Toggling containers with docker-compose in NodeJS

This repository contains an example on how to use the `docker-compose` npm library to toggle containers in NodeJS, which you can find [here](https://www.npmjs.com/package/docker-compose).  

## Initial Setup

- Clone this repository with `git clone https://github.com/clemenkok/DockerComposeforNodeJS`. 
- Type the following into your terminal: `cd DockerComposeforNodeJS`.
- Open your code editor. I use Visual Studio Code, so type in `code .`.
- Do `npm install`.
- Toggle the container you want with either of the following commands: `node app.js nodejs` to fire up the Node JS container, and `node app.js db` to fire up the PostgreSQL container.
- Type in `docker ps` in the terminal: you should see the container you selected!  
- Use `docker stop <CONTAINER-ID>` to stop the container you started.

## TODO

- To modify with correct images
- To test `depends-on` keyword
- Add GUI
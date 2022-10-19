# Docker Overview

- Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. 
- With Docker, you can manage your infrastructure in the same ways you manage your applications. 
- By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.`

## Virtual Machine Vs Docker Containers
![vm vs docker](/images/2-vm.png)

## The Docker platform

- Docker provides the ability to package and run an application in a loosely isolated environment called a container. 
- The isolation and security allow you to run many containers simultaneously on a given host.

## Docker Engine

_Docker Engine_ is a client-server application with these major components:

- A server which is a type of long-running program called a daemon process (the dockerd command). 
- A REST API which specifies interfaces that programs can use to talk to the daemon and instruct it what to do. 
- A command line interface (CLI) client (the docker command).

## What can I use Docker for?

### 

Fast, consistent delivery of your applications

Docker streamlines the development lifecycle by allowing developers to work in standardized environments using local containers which provide your applications and services. Containers are great for continuous integration and continuous delivery (CI/CD) workflows.

Consider the following example scenario:

- Your developers write code locally and share their work with their colleagues using Docker containers.
- They use Docker to push their applications into a test environment and execute automated and manual tests.
- When developers find bugs, they can fix them in the development environment and redeploy them to the test environment for testing and validation.
- When testing is complete, getting the fix to the customer is as simple as pushing the updated image to the production environment.

## Docker architecture

### 

- Docker uses a client-server architecture. 
- The Docker _client_ talks to the Docker _daemon_, which does the heavy lifting of building, running, and distributing your Docker containers. 
- The Docker client and daemon _can_ run on the same system, or you can connect a Docker client to a remote Docker daemon. 
- The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface

### The Docker daemon


- The Docker daemon (dockerd) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes. 
- A daemon can also communicate with other daemons to manage Docker services.

### The Docker client

- The Docker client (docker) is the primary way that many Docker users interact with Docker. 
- When you use commands such as docker run, the client sends these commands to dockerd, which carries them out. 
- The docker command uses the Docker API. The Docker client can communicate with more than one daemon.

### Docker registries

- A Docker _registry_ stores Docker images. 
- Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default. 
- You can even run your private registry. 
- If you use Docker Datacenter (DDC), it includes Docker Trusted Registry (DTR).

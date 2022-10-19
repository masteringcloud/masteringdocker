# <font color="Orangered"> Docker architecture </font>


![Architecture Image](/images/docker-arch.png)

- Docker uses a client-server architecture. 
- The Docker _client_ talks to the Docker _daemon_, which does the heavy lifting of building, running, and distributing your Docker containers. 
- The Docker client and daemon _can_ run on the same system, or you can connect a Docker client to a remote Docker daemon. 
- The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface

## <font color="Orangered"> Docker Daemon </font>

- The Docker daemon (dockerd) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes. 
- A daemon can also communicate with other daemons to manage Docker services.

## <font color="Orangered"> The Docker client </font>

- The Docker client (docker) is the primary way that many Docker users interact with Docker. 
- When you use commands such as docker run | pull | exec .., the client sends these commands to Docker daemon. 
- The command uses the Docker API. The Docker client can communicate with more than one daemon.

## <font color="Orangered"> Docker registries </font>

- A Docker _registry_ stores Docker images. 
- Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default.
- You can even run your private registry. If you use Docker Datacenter (DDC), it includes Docker Trusted Registry (DTR).
- AWS ECR is Amazon own Image Repository

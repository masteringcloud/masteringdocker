# Docker Swarm

## Feature highlights
- Cluster management integrated with Docker Engine
- Decentralized design
- Scaling
- Desired state reconciliation
- Multi-host networking
- Service discovery
- Load balancing
- Rolling updates

## Swarm mode key concepts

### **What is a swarm?** 
- The cluster management and orchestration features embedded in the Docker Engine are built using swarmkit. Swarmkit is a separate project which implements Docker’s orchestration layer and is used directly within Docker.
![swarm-architecture](/images/12-swarm.png)
- A swarm consists of multiple Docker hosts which run in swarm mode and act as managers (to manage membership and delegation) and workers (which run swarm services)

- A given Docker host can be a manager, a worker, or perform both roles.

- One of the key advantages of swarm services over standalone containers is that you can modify a service’s configuration, including the networks and volumes it is connected to, without the need to manually restart the service.

- Docker will update the configuration, stop the service tasks with the out of date configuration, and create new ones matching the desired configuration.

## **Nodes**

- A node is an instance of the Docker engine participating in the swarm. You can also think of this as a Docker node.

- **Worker nodes** receive and execute tasks dispatched from manager nodes. 
    - By default manager nodes also run services as worker nodes, but you can configure them to run manager tasks exclusively and be manager-only nodes. 
    - An agent runs on each worker node and reports on the tasks assigned to it.

## **Services and tasks**
- A service is the definition of the tasks to execute on the manager or worker nodes.
- When you create a service, you specify which container image to use and which commands to execute inside running containers.

- In the **replicated services** model, the swarm manager distributes a specific number of replica tasks among the nodes based upon the scale you set in the desired state.

- For **global services**, the swarm runs one task for the service on every available node in the cluster.

## **Load balancing**
- The swarm manager uses **ingress load balancing** to expose the services you want to make available externally to the swarm.

- The swarm manager can automatically assign the service a PublishedPort or you can configure a PublishedPort for the service

- If you do not specify a port, the swarm manager assigns the service a port in the **30000-32767** range
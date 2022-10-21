# Working With Swarm

## Create a swarm

```console
$ docker swarm init --advertise-addr <MANAGER-IP>
$ docker info | grep -i swarm 
```

The output includes the commands to join new nodes to the swarm. Nodes will join as managers or workers depending on the value for the --token flag.

## Adding Node to the swarm

- you will get the joining token when you initialize the swam

- Execute below command to get the token if you missed to record in previous step 

```console
$ docker swarm join-token worker
```
- Execute the generated token on all the worker nodes 

- To verify of all the nodes are added to swarm, you can execute below command 
```console 
$ docker node ls 
```

## Creating a Service 

All the commands will be executed on Master Node 

```console
$ docker service create --replicas 1 --name helloworld nginx
```

 List the services 
```console
$ docker service ls
```
Inspect The service

```console  
$ docker service inspect --pretty helloworld
```
view where the task is running on which node 
```console 
$ docker service ps helloworld
```

## Scale the service in the swarm

```console
$ docker service scale <SERVICE-ID>=<NUMBER-OF-TASKS>
$ docker service scale helloworld=5
```
see the updated task list

```console
$ docker service ps helloworld
```

## Apply rolling updates to a service

```console
$ docker service create \
  --replicas 3 \
  --name redis \
  --update-delay 10s \
  redis:3.0.6
```
Inspecting the service 

```console
$ docker service inspect --pretty redis
```

Updating the container Image 

```console
$ docker service update --image redis:3.0.7 redis
```
- The scheduler applies rolling updates as follows by default:

   - Stop the first task.
   - Schedule update for the stopped task.
   - Start the container for the updated task.
   -  If the update to a task returns RUNNING, wait for the       specified delay period then start the next task.
   - If, at any time during the update, a task returns FAILED, pause the update.

Verify if the new image is uplied 

```console 
$ docker service inspect --pretty redis
$ docker service ps redis
```
## Delete the service running on the swarm

```console
$ docker service rm helloworld
```
## Drain a node on the swarm

Verify that all your nodes are actively available.
```console
$ docker node ls 
```
If you aren’t still running the redis service from the previos step, create a service 

```console
$  docker service create --replicas 3 --name redis --update-delay 10s redis:3.0.6
```

To verify the service 
```console
$ docker service ps redis
```
To drain a node that had a task assigned to it
here, worker1 is the name of the node, use your node name inspeted of worker1
```console
$  docker node update --availability drain worker1
```

Inspect the node to check its availability
```console
$  docker node inspect --pretty worker1
```
The drained node shows Drain for AVAILABILITY.

To return the drained node to an active state:

 ```console
 $ docker node update --availability active worker1
 $ docker node inspect --pretty worker1
  ```

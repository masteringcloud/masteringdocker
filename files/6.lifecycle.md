# Docker container Lifecycle Management

The lifecycle of a docker container consists of five states:

- Created state
- Running state
- Paused state/unpaused state
- Stopped state
- Killed/Deleted state

![lifcycle](/images/3-lfc.png)

**Created state**

In the created state, a docker container is created from a docker image.
```
docker create --name <name-of-container> <docker-image-name>
docker create --name test nginx
```

**Running state**

In the running state, the docker container starts executing the commands mentioned in the image. To run a docker container, use the docker run command.
```
docker run <container-id>
docker run <container-name>
```

The docker run command creates a container if it is not present. In this case, the creation of the container can be skipped.

**Paused state**

In the paused state, the current executing command in the docker container is paused. Use the docker pause command to pause a running container.
```
docker pause container <container-id or container-name>
```

Note: docker pause pauses all the processes in the container. It sends the SIGSTOP signal to pause the processes in the container.

**Unpaused state**

In the unpaused state, the paused container resumes executing the commands once it is unpaused.

Use the docker unpause command to resume a paused container.

Then, Docker sends the SIGCONT signal to resume the process.
```
docker unpause <container-id or container-name>
```

**Stopped state**

In the stopped state, the container’s main process is shutdown gracefully. Docker sends SIGTERM for graceful shutdown, and if needed, SIGKILL, to kill the container’s main process.

Use the docker stop command to stop a container.
```
docker stop <container-id or container-name>
```
Restarting a docker container would translate to docker stop, then docker run, i.e., stop and run phases.

**Killed/Deleted state**

In the killed state, the container’s main processes are shutdown abruptly. Docker sends a SIGKILL signal to kill the container’s main process.
```
docker kill <container-id or container-name>
```
## Complete lifecycle of a Docker container

![Complete lifecycle of a Docker container](/images/4-lfc1.png)


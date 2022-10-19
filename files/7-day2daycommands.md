# Docker Commands Examples 

## Exploring Docker information

Show Docker inforation
```
docker info
```

List all containers (stopped and running)
```
docker ps -a 
    -a --all
```
List images 
```
# see all the sub-commands and options
docker images --help 

# list all images
docker images

# List only image ID's
docker images -q

# list images with sha values 
docker images --digests 
```

Creating a contaniners 


# see all the sub-commands and options
```console
$ docker run --help
```
# Rining a container 
```console
$ docker run -itd nginx

    - i --Inteactive 
    - t --terminal 
    - d -- detached mode 
```
# List the created container 
```console
$ docker ps 
```
# list running , exited, stopped containers

```console
$ docker ps -a 
```

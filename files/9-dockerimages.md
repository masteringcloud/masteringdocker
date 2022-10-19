# Docker Images

# Docker Image Creation Process

![DockerImage Creation Flow](/images/6-Image-Creation.png)

# Example Docker File

Create a Dockerfile and paste below lines

```Dockerfile
FROM ubuntu:trusty
RUN apt-get update -y && apt-get install -y apache2
WORKDIR /var/www/html/
COPY . . 
CMD ["apachectl", "-D","FOREGROUND"]
EXPOSE 80
```
## Building an Image 

```console
$ docker build --tag test-image .
    --tag = name of the image 
    . - current working dir
 
```
## Pushing the Image to Remote Docker Hub 

we should make sure that local image name is matching the remote repo name 

```console
$ docker tag <sourseImageName> <RemoteRepoName>
$ docker login 
$ docker push  
```

| Instruction      |      Description                                                                                                        |  
|------------------|:-----------------------------------------------------------------------------------------------------------------------:|
| FROM             | Sets the Base Image for subsequent instructions.                                                                        |
| RUN              | execute any commands in a new layer on top of the current image and commit the results.                                 |
| ADD              | copies new files, directories or remote file to container. Invalidates caches. Avoid ADD and use COPY instead.          |
| COPY             | copies new files or directories to container.                                                                           |
| EXPOSE           | informs Docker that the container listens on the specified network ports at runtime.                                    |
| ENV              | sets environment variable.                                                                                              |
| ARG              | defines a build-time variable.                                                                                          |
| VOLUME           | creates a mount point for externally mounted volumes or other containers.                                               |
| USER             | sets the user name for following RUN / CMD / ENTRYPOINT commands.                                                       |
| WORKDIR          | Sets the working directory.                                                                                             |
| CMD              | provide defaults for an executing container..                                                                           |
| ENTRYPOINT       | configures a container that will run as an executable.                                                                  |
| ONBUILD          | adds a trigger instruction when the image is used as the base for another build.                                        |
| STOPSIGNAL       | sets the system call signal that will be sent to the container to exit.                                                 |
| LABEL            | apply key/value metadata to your images, containers, or daemons..                                                       |
| SHELL            | override default shell is used by docker to run commands..                                                              |
|:----------------:|:m-----------------------------------------------------------------------------------------------------------------------:|

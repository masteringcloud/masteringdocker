# Docker Volumes

- Volumes are the preferred mechanism for persisting data generated by and used by Docker containers. 
- While [bind mounts](bind-mounts.md) are dependent on the
directory structure and OS of the host machine, volumes are completely managed by Docker. 

Volumes have several advantages over bind mounts:

- Volumes are easier to back up or migrate than bind mounts.
- You can manage volumes using Docker CLI commands or the Docker API.
- Volumes work on both Linux and Windows containers.
- Volumes can be more safely shared among multiple containers.
- Volume drivers let you store volumes on remote hosts or cloud providers, to encrypt the contents of volumes, or to add other functionality.
- New volumes can have their content pre-populated by a container.
- Volumes on Docker Desktop have much higher performance than bind mounts from
  Mac and Windows hosts.


![volumes on the Docker host](/images/5-volumestype.png)

If your container generates non-persistent state data, consider using a
[tmpfs mount](tmpfs.md) to avoid storing the data anywhere permanently, and to
increase the container's performance by avoiding writing into the container's
writable layer.

Volumes use `rprivate` bind propagation, and bind propagation is not
configurable for volumes.


### Differences between `-v` and `--mount` behavior

As opposed to bind mounts, all options for volumes are available for both
`--mount` and `-v` flags.

When using volumes with services, only `--mount` is supported.

## Create and manage volumes

Unlike a bind mount, you can create and manage volumes outside the scope of any
container.

**Create a volume**:

```console
$ docker volume create my-vol
```

**List volumes**:

```console
$ docker volume ls

local               my-vol
```

**Inspect a volume**:

```console
$ docker volume inspect my-vol
[
    {
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
        "Name": "my-vol",
        "Options": {},
        "Scope": "local"
    }
]
```

**Remove a volume**:

```console
$ docker volume rm my-vol
```

## Start a container with a volume

If you start a container with a volume that does not yet exist, Docker creates
the volume for you. The following example mounts the volume `myvol2` into
`/app/` in the container.

The `-v` and `--mount` examples below produce the same result. You can't run
them both unless you remove the `devtest` container and the `myvol2` volume
after running the first one.


```console
$ docker run -d \
  --name devtest \
  --mount source=myvol2,target=/app \
  nginx:latest
```

</div><!--mount-->
<div id="v-run" class="tab-pane fade" markdown="1">

```console
$ docker run -d \
  --name devtest \
  -v myvol2:/app \
  nginx:latest
```

</div><!--volume-->
</div><!--tab-content-->

Use `docker inspect devtest` to verify that the volume was created and mounted
correctly. Look for the `Mounts` section:

```json
"Mounts": [
    {
        "Type": "volume",
        "Name": "myvol2",
        "Source": "/var/lib/docker/volumes/myvol2/_data",
        "Destination": "/app",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
],
```

This shows that the mount is a volume, it shows the correct source and
destination, and that the mount is read-write.

Stop the container and remove the volume. Note volume removal is a separate
step.

```console
$ docker container stop devtest

$ docker container rm devtest

$ docker volume rm myvol2
```

## Use a volume with docker-compose

A single docker compose service with a volume looks like this:

```yaml
version: "{{ site.compose_file_v3 }}"
services:
  frontend:
    image: node:lts
    volumes:
      - myapp:/home/node/app
volumes:
  myapp:
```

On the first invocation of `docker-compose up` the volume will be created. The same
volume will be reused on following invocations.

A volume may be created directly outside of compose with `docker volume create` and
then referenced inside `docker-compose.yml` as follows:

```yaml
version: "{{ site.compose_file_v3 }}"
services:
  frontend:
    image: node:lts
    volumes:
      - myapp:/home/node/app
volumes:
  myapp:
    external: true
```

For more information about using volumes with compose see
[the compose reference](../compose/compose-file/compose-file-v3.md#volume-configuration-reference).

### Start a service with volumes

When you start a service and define a volume, each service container uses its own
local volume. None of the containers can share this data if you use the `local`
volume driver, but some volume drivers do support shared storage. Docker for AWS and
Docker for Azure both support persistent storage using the Cloudstor plugin.

The following example starts a `nginx` service with four replicas, each of which
uses a local volume called `myvol2`.

```console
$ docker service create -d \
  --replicas=4 \
  --name devtest-service \
  --mount source=myvol2,target=/app \
  nginx:latest
```

Use `docker service ps devtest-service` to verify that the service is running:

```console
$ docker service ps devtest-service

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
4d7oz1j85wwn        devtest-service.1   nginx:latest        moby                Running             Running 14 seconds ago
```

Remove the service, which stops all its tasks:

```console
$ docker service rm devtest-service
```

Removing the service does not remove any volumes created by the service.
Volume removal is a separate step.

#### Syntax differences for services

The `docker service create` command does not support the `-v` or `--volume` flag.
When mounting a volume into a service's containers, you must use the `--mount`
flag.

### Populate a volume using a container

If you start a container which creates a new volume, as above, and the container
has files or directories in the directory to be mounted (such as `/app/` above),
the directory's contents are copied into the volume. The container then
mounts and uses the volume, and other containers which use the volume also
have access to the pre-populated content.

To illustrate this, this example starts an `nginx` container and populates the
new volume `nginx-vol` with the contents of the container's
`/usr/share/nginx/html` directory, which is where Nginx stores its default HTML
content.

The `--mount` and `-v` examples have the same end result.


```console
$ docker run -d \
  --name=nginxtest \
  --mount source=nginx-vol,destination=/usr/share/nginx/html \
  nginx:latest
```


```console
$ docker run -d \
  --name=nginxtest \
  -v nginx-vol:/usr/share/nginx/html \
  nginx:latest
```

After running either of these examples, run the following commands to clean up
the containers and volumes.  Note volume removal is a separate step.


```console
$ docker container stop nginxtest

$ docker container rm nginxtest

$ docker volume rm nginx-vol
```

## Use a read-only volume

For some development applications, the container needs to write into the bind
mount so that changes are propagated back to the Docker host. At other times,
the container only needs read access to the data. Remember that multiple
containers can mount the same volume, and it can be mounted read-write for some
of them and read-only for others, at the same time.

This example modifies the one above but mounts the directory as a read-only
volume, by adding `ro` to the (empty by default) list of options, after the
mount point within the container. Where multiple options are present, separate
them by commas.


```console
$ docker run -d \
  --name=nginxtest \
  --mount source=nginx-vol,destination=/usr/share/nginx/html,readonly \
  nginx:latest
```

</div><!--mount-->
<div id="v-readonly" class="tab-pane fade" markdown="1">

```console
$ docker run -d \
  --name=nginxtest \
  -v nginx-vol:/usr/share/nginx/html:ro \
  nginx:latest
```

Use `docker inspect nginxtest` to verify that the readonly mount was created
correctly. Look for the `Mounts` section:

```json
"Mounts": [
    {
        "Type": "volume",
        "Name": "nginx-vol",
        "Source": "/var/lib/docker/volumes/nginx-vol/_data",
        "Destination": "/usr/share/nginx/html",
        "Driver": "local",
        "Mode": "",
        "RW": false,
        "Propagation": ""
    }
],
```

Stop and remove the container, and remove the volume. Volume removal is a
separate step.

```console
$ docker container stop nginxtest

$ docker container rm nginxtest

$ docker volume rm nginx-vol
```


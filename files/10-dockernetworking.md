# Docker Networking 

## Docker Network Drivers

- **User-defined bridge** networks are best when you need multiple containers to communicate on the same Docker host.

    ![bridgenetwork](/images/7-bridgenetwork.png)

- **Host** networks are best when the network stack should not be isolated from the Docker host, but you want other aspects of the container to be isolated.
![hostnetwork](/images/8-hostnetwork.png)]
- **Overlay** networks are best when you need containers running on different Docker hosts to communicate, or when multiple applications work together using swarm services.

    ![overlaynetwork](/images/9-overlaynetwork.png)

- **Macvlan** networks are best when you are migrating from a VM setup or need your containers to look like physical hosts on your network, each with a unique MAC address.

![macvlan](/images/10-macvlan.png)
![macvlan](/images/11-macvlan-.png)

Third-party network plugins allow you to integrate Docker with specialized network stacks.


## Networking Commands 

```console
$ docker network ls 
$ docker network inspect bridge
```
Creating Docker network 
```console
$ docker network create -d bridge my-bridge-network
$ docker network create -d overlay my-multihost-network
```
Attacahing the container to a custom network 
```console
$ docker run -itd --network=mynet busybox
```
Specify advanced options

```console
docker network create --driver=bridge --subnet=192.168.0.0/16 br0
```
Additionally, you also specify the --gateway --ip-range and --aux-address options.
```console
$ docker network create \
  --driver=bridge \
  --subnet=172.28.0.0/16 \
  --ip-range=172.28.5.0/24 \
  --gateway=172.28.5.254 \
  br0
```
# Get Docker Engine - Community for CentOS

## OS requirements

- To install Docker Engine - Community, you need a maintained version of CentOS 7

- The centos-extras repository must be enabled. This repository is enabled by default, but if you have disabled it, you need to re-enable it.

- The overlay2 storage driver is recommended.

## Install Docker Engine - Community

- Configiure Docker **repository** and install from them. This is easy and recommended approach.
- Download **RPMs packages** manually and install. This installation is recommended for systems without internet connections
- Install using docker **script**

## Install using the repository

Before you install Docker Engine - Community for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

## SET UP THE REPOSITORY

1. Install required packages

    ```console
    $ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
    ```
2. Set up the stable repository

    ```console
    $ yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    ```

## INSTALL DOCKER ENGINE - COMMUNITY

1. Install the latest version of Docker Engine - Community and containerd

    ```console
    $ yum install docker-ce docker-ce-cli containerd.io
    ```

2. Start Docker
    ```console
    $ systemctl start docker
    ```

3. Verify that Docker Engine - Community is installed correctly by running the hello-world image.
    ```console
    $ docker run hello-world
    ```

## Install using the convenience script

```console
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```

## If you are intrested in installing docker on other platforms refer below documentation

[Official Docker Documentation](https://docs.docker.com/engine/install/)
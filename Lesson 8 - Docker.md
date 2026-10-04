
![[images/docker-logo.png|170]]
#### Containerization vs Virtualization
**Virtualization** will virtualizes the hardware like a real machine, meaning it will emulate physical components like RAM, CPU,... and each VMs will have it own OS. But Virtualization are resource intensive and can cause overhead if there are too many VMs. This is why we have **Containerization**, it only virtualize at the OS level so it will much lighter and faster than a VM, but that doesn't mean containerization is better than virtualization, it isolation is weaker than virtualization meaning it can be exploits to access things you don't want to.

#### Docker
There are a lots of containerization tools, but **Docker** is among of popular one aside from:
- Podman
- containerd

#### Setup Docker
Recommend to install docker from a linux machine instead of Windows to save disk space and RAM:

[Install in Linux](https://docs.docker.com/engine/install/)
[Install on Windows](https://docs.docker.com/desktop/setup/install/windows-install/)
[Install on MacOS](https://docs.docker.com/desktop/setup/install/mac-install/)

#### Images
To start a containers, we need to give it a templates aka **images**. Container is basically an image that's actively running, you boot up a container from an image and you can create multiple separate containers all from the same image.

Right now we will try to pull `docker/getting-started` image to see if docker is working.
```sh
$ docker pull docker/getting-started
```

Then use `iamages` command to view all images
```sh
$ docker images
```

#### Containers
After you've pulled the `docker/getting-started`, let's use it to run a new container. 

The `docker run` command starts a new container from a image:
```sh
$ docker run -d -p host_port:container_port namespace/name:tag
```

- `-d`: Run in detached mode (doesn't block your terminal)
-  `-p`: Publish a container's port to the host (forwarding)
- `host_port`: The port on your local machine
- `container_port`: The port inside the container
- `namespace/name`: The name of the image (usually in the format username/repo)
- `tag`: The version of the image (often latest)

With that we will have this command to run our image:
```sh
$ docker run -d -p 9999:80 docker/getting-started:latest
```

Next use this command to see the container running
```sh
$ docker ps
```

On one of the columns you should see this
```
PORTS
0.0.0.0:9999->80/tcp
```
This mean that port `9999` on your localhost machine is being forwarded to port `80` on the running container. Port `80` is used for HTTP web traffic. Navigate to `http://localhost:9999` and you should see a webpage served from the container!

When you can start a container, you should be able to stop it and there are 2 main ways:
- `docker stop`: This stops the container by issuing a `SIGTERM` signal to the container. You'll typically want to use `docker stop`.
- `docker kill`: This stops the container by issuing a `SIGKILL` signal to the container. This is a more forceful way to stop a container, and should be used as a last resort.

First run `docker ps` and copy  the container ID then run
```sh
$ docker stop CONTAINER_ID
```

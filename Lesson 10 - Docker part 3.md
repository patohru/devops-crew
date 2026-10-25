### Networking
#### Offline
Normally a container will have access to external networks and we can force that into offline mode (No access to internet).

But why would we want to turn off networking? Usually it's for security reasons. You might want to remove the network connection from a container in one of these scenarios:
- You're running 3rd party code that you don't trust, and it shouldn't need network access
- You're building an e-learning site, and you're allowing students to execute code on your machines
- You know a container has a virus that's sending malicious requests over the internet, and you want to do an audit

#### Network None
The `docker run` command has  a `--network none` flag that makes it so that the container  can't network with the outside world.

We can also use `docker network disconnect` for a container that is running without restart

```sh
docker network disconnect bridge <container_id_or_name>
```

```sh
docker network disconnect <network_name> <container_id_or_name>
```

Now let's try to create a container and break it network to make sure that it can't reach the outside world.
The `ping` command allows you to check for connectivity to a host. For example we can ping `google.com`
```
ping google.com
```
If everything is working, we'll see a response like:
```
64 bytes from nchkgb-aj-in-f14.1e100.net (142.250.198.142): icmp_seq=1 ttl=114 time=72.9 ms
64 bytes from nchkgb-aj-in-f14.1e100.net (142.250.198.142): icmp_seq=2 ttl=114 time=73.8 ms
64 bytes from nchkgb-aj-in-f14.1e100.net (142.250.198.142): icmp_seq=3 ttl=114 time=73.1 ms
```
You can stop the `ping` command with `Ctrl + c`

Now start a new "Getting Started" container in `--network none` mode:
```sh
docker run -d --network none docker/getting-started
```
Run the `ping` command with a timeout of 2 seconds inside the container
```sh
docker exec CONTAINER_ID ping google.com -W 2
```
If all goes well, the program should hang for 2 seconds then return with an error message

#### Load Balancers
A load balancer like the name suggests, it balances a load of network traffic across some number of servers. Think of a huge website like Youtube, Google,... There's no way that a single server could handle all of the request for entire world. They uses load balancers to route requests to different servers. 

A central server, called the "load balancer", receives traffic from users (aka clients), then routes those requests to different backend applications servers. In the case of Google, this splits the world's traffic across potentially many different thousands of computers.

A good load balancer sends new traffic to servers that have lower current resource utilization (CPU and memory). The goal is to "balance the load" so that no single backend server becomes overwhelmed. There are [many strategies](https://www.cloudflare.com/learning/performance/types-of-load-balancing-algorithms/) that load balancers use, but a simple strategy is the "round robin" where request are simply routed one after the other to different backend servers.

#### Application Servers
First we need to start some application servers so that our load balancer has somewhere to send the traffic. We will use [Caddy](https://caddyserver.com/), an awesome opensource load balancer and web server. [Nginx](https://nginx.org/) and [Apache](https://httpd.apache.org/) are other popular alternatives that do similar things, but Caddy is a modern version written in Go.

We will now create 2 application server that will serve different HTML webpage.

First pull down the caddy image:
```sh
docker pull caddy
```
Create an `index1.html` in your working directory:
```html
<html>
  <body>
    <h1>Hello from server 1</h1>
  </body>
</html>
```
Create an `index2.html` in your working directory:
```html
<html>
  <body>
    <h1>Hello from server 2</h1>
  </body>
</html>
```
Run a container for `index1.html` on port `8881`
```sh
docker run -d -p 8881:80 -v "$PWD/index1.html:/usr/share/caddy/index.html" caddy
```
Run a container for `index2.html` on port `8882`
```sh
docker run -d -p 8882:80 -v "$PWD/index2.html:/usr/share/caddy/index.html" caddy
```
Navigate to `localhost:8881` and `localhost:8882` to check it work.

#### Custom Network
We can create custom bridge networks so that containers can communicate with each other if we want them to, but still otherwise remain isolated.

Let's create a custom bridge network called "caddytest"
```sh
docker network create caddytest
```
See if it worked by listing all the networks:
```sh
docker network ls
```
Next stop the containers and spin up new ones with the same `docker run` command from the previous lesson, but this time, make sure you attach them to the `caddytest` network.
- Use the `--network caddytest` flag to attach them to the network
- Use the `--name` flag to name them `caddy1` and `caddy2`
```sh
docker run -d --name caddy1 --network caddytest -v "$PWD/index1.html:/usr/share/caddy/index.html" caddy
```
```sh
docker run -d --name caddy2 --network caddytest -v "$PWD/index2.html:/usr/share/caddy/index.html" caddy
```
Create another "getting started" container on the same network and start a shell session with it:
```sh
docker run -it --network caddytest docker/getting-started /bin/sh
```
Giving our container some names, `caddy1` and `caddy2`, and providing a bridge network, Docker has setup name resolution for us. The container names resolve to the individual containers from all other containers on the network.

Within your "getting started" container shell, `curl` the first container:
```sh
curl caddy1
```
```sh
curl caddy2
```
If you get the HTML responses that you expect, `exit` out of your shell session.
#### Configuring the Load Balancer
Once confirmed that we have 2 application servers (Caddy) working properly on a custom bridge network. Let's create a load balancer that balances network requests between the two! We'll use a round-robin balancing strategy, so each request should route back and forth between the servers.
#### Caddyfile
Caddy work great as a file server, which is what our little HTML servers are, but it also works great as a load balancer! To use Caddy as a laod balancer we'll need to create a custom `Caddyfile` to tell Caddy how we want it to balance the traffic. It's just a config file for Caddy

Create a new file in your local directory called `Caddyfile`:
```
localhost:80

reverse_proxy caddy1:80 caddy2:80 {
    lb_policy       round_robin
}
```
This tell Caddy to run on `localhost:80`, and to round-robin any incoming traffic to `caddy1:80` and `caddy2:80`. Remember, this only works because we're going to run the load balancer on the same network, so `caddy1` and `caddy2` will automatically resolve to our application server container.

Start the load balancer container on port `8080`. Instead of an `index.html`, give it our custom `Caddyfile`
```sh
docker run -d --network caddytest -p 8880:80 -v "$PWD/Caddyfile:/etc/caddy/Caddyfile" caddy
```

Hit the load balancer on `http://localhost:8080/`! You should either get a response from server 1 or server 2, and if you hard refresh the page, it should swap back and forth.

If it's not swapping properly, try using `curl` instead, your browser might be caching the HTML

### Debug
When containers are running in detached mode with the `-d` flag, you don't see any output in your terminal, which is nice for keeping your terminal clean, but what if something goes wrong?

That's what `docker logs` are here for
```sh
docker logs [OPTIONS] CONTAINER
```
You can specify some flag to make the logs easier to read
```sh
docker logs --tail 5 CONTAINER
```
Notice that if you run `docker logs` over and over again, you'll see that the output is different. That's because you're only getting the most recent logs each time. So you specify `-f` flag to the command to follow the logs in real-time.
#### Stats
We now know how to inspect container logs, but how do we watch the resource utilization?

The `docker stats` command give you a live data stream of resource usage for running containers.
```sh
docker stats [OPTIONS] [CONTAINER...]
```
#### Top
The `docker top` command shows the running processes inside a container
```sh
docekr top CONTAINER [ps OPTIONS]
```
#### Resource Limits
When you notice a container's using too many resources, if you don't have the time or ability to "fix" the code, you can limit the resources the container has available. The `docker run` command has a few options for limiting resources:
- `--memory`: Limit the memory available to the container
- `--cpu`: Limit the CPU time available to the containter

You can use this command to create a stress test container with the CPU limit to `0.25` (1/4th of a CPU core):
```sh
docker run -d --cpus="0.25" --name cpu-stress alexeiled/stress-ng --cpu 2 --timeout 10m
```

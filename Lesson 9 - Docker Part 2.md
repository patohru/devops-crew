### Common TCP ports
1. 80 (HTTP): AKA Hyper Text Transfer Protocol are being used to transfer data without TLS encryption which should not be used by default by today standard but it still can be use to move to another more secure one.
2. 433 (HTTPS): Another Hyper Text Transfer but more secure by using TLS encryption most website are using this instead of just HTTP.
3. 23 (Telnet): A network protocol that allows unsecure connection to another computers in CLI. Strangely, some devices like routers or switches are still using this.
4. 22 (SSH): Secure Shell, an replacement for Telnet (Read more [here](https://datatracker.ietf.org/doc/html/rfc4253)).
5. 21 (FTP): A protocol that help transferring file and like Telnet is really insecure.
6. 25 (SMTP): Simple Mail Transfer Protocol and also insecure.
### Volumes
By default, Docker containers don't keep any state from past containers. For example: 
1. Start a container from an new image
2. Make some changes to the filesystem (like installing a new package) in that container
3. Stop the container
4. Start a new container from the same image
5. The new container does not have the changes I made in step 2
By that, Docker does have ways to support "persistent state" through storage volumes. They are basically a filesystem that lives outside of container, but can be accessed by the container

#### Run Ghost
Now we will test this by using [Ghost](https://ghost.org/) (It's an open-source blogging software, kinda like WordPress)

1. Create a new volume called `ghost-data`
```sh
docker volume create ghost-vol
```
2. Check if it show up
```sh
docker volume ls
```
3. Inspect the volume to see where it is on your machine
```sh
docker volume inspect ghost-vol
```

After checking the volume is created, time to run Ghost in Docker
1. Pull the ghost image from Docker hub
```sh
docker pull ghost
```
2. Run the Ghost image in a new container
```sh
docker run -d \
  -e NODE_ENV=development \
  -e url=http://localhost:3001 \
  -e database__connection__filename=/var/lib/ghost/content/data/ghost-dev.db \
  -p 3001:2368 \
  -v ghost-vol:/var/lib/ghost/content \
  ghost
```
- `-d` runs the image in detached mode to avoid blocking the terminal.
- `-e NODE_ENV=development` sets an [environment variable](https://en.wikipedia.org/wiki/Environment_variable) within the container. This tells Ghost to run in "development" mode (rather than "production", for instance)
- `-e url=http://localhost:3001` sets another environment variable, this one tells Ghost that we want to be able to access Ghost via a URL on our host machine.
- `-e database__connection__filename=/var/lib/ghost/content/data/ghost-dev.db` specifies where Ghost stores its SQLite database.
- We've used `-p` before. `-p 3001:2368` does some [port-forwarding](https://en.wikipedia.org/wiki/Port_forwarding) between the container and our host machine.
- `-v ghost-data:/var/lib/ghost/content` mounts the volume we created at Ghost's content directory, so its files, including the database, survive when we replace the container.
3. Navigate to `http://localhost:3001` in your browser, you should your new Ghost.
#### Creating a new Website
Next we will create a Website in Ghost
1. Navigate to Ghost's admin panel: `http://localhost:3001/ghost/#/setup` and create new website
2. Create a new post and publish it.
3. Go back to the homepage to see your post: `http://localhost:3001/`

#### Persist
To check if our volume is working is to create a new container. If it's working, our new container will still serve the post we created because it will load it from the volume
1. Get your container ID:
```sh
docker ps
```
2. Stop and remove the container with the `rm` command 
```sh
docker stop CONTAINER_ID
docker rm CONTAINER_ID
```
3. Start a new container from the same image and same volume using the same command in [Run Ghost](#run-ghost)
#### Remove a Volume
1. Use `docker ps -a` to see all containers, even those that aren't running.
2. Stop the running Ghost container
3. Remove the ghost container.
4. Remove the `ghost-vol` volume using `docker volume rm`
5. Use the same command to run Ghost again after you checked everything is removed.
Navigate to `http://localhost:3001/` and you should see a fresh Ghost site without our posts, but shouldn't the command give us the error?
If you run `docker volume ls` you can see ghost-data is back even though we removed it? Turn out the `-v ghost-data:/var/lib/ghost/content` flag binds to a "ghost-data" volume if it exists, otherwise, it creates it automatically!

### Execute
When deploying to docker, I'll usually just let the container do it thing. For example, the Ghost container we just ran started up it own web server based on the image configuration). We didn't need to run any manual commands in addition to just starting the container.
Though it is possible to run commands inside a container. Kinda like we ssh into a remote server and running commands.
1. List running containers:
```sh
docker ps
```
2. Start up the "getting started" container
```
docker run -d -p 8965:80 docker/getting-started
```
3. Run an `ls` command from inside the container using the `docker exec` command
```
docker exec CONTAINER_ID ls
```
4. Create a new `hacker.log` file in the working directory of the container inside the container
5. Run the `ls` again to make sure it created.
#### Exec Netstat
The netstat command shows us which programs are bound to which ports. We're looking for the process bound to port 80, that is, the one serving the webpage.

Run the `netstat -ltnp` command inside the container with docker exec, then answer the question.

#### Live Shell
It would be more convenient to start a shell session running within the container itself instead of run one command then it exit. That's where the `-i` and `-t` flags come in:
- `-i` make the `exec` command interactive
- `-t` gives us a tty interface
- Running `/bin/sh` gives us a shell session inside the container
For Example: 
```
dcoker exec -it CONTAINER_ID /bin/sh
```
#### Assignment
1. Start a new shell session inside the getting started container
2. Change into the `/usr/share/nginx/html/` directory. If you're in the right spot, this is the root of files served by the web server.
3.  Open the website in your browser at `http://localhost:8965`. Notice it automatically redirects you to the `/tutorial` page.
4.  `cd` into the tutorial directory.
5.  Let's "hack" the site! Overwrite the contents of `index.html` with the string `I hacked you!`:
```sh
echo "I hacked you!" > index.html
```
6. Refresh the page to make sure it worked.
7. Exit the container shell session with the `exit` command.
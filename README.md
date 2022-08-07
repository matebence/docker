## Docker

What is Docker?
Carves up a computer into sealed containers that run our code

What is container?
A self contained sealed unit of software

A container include:
- Code
- Configs
- Processes
- Networkingcat
- Dependencies
- Operating system

![Docker](https://raw.githubusercontent.com/matebence/docker/master/docker.png)

### Instalation

```bash
### Docker and docker compose prerequisites
sudo apt-get install apt-transport-https curl gnupg ca-certificates lsb-release software-properties-common

### Download the docker gpg file to Ubuntu
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu `lsb_release -cs` test"

### Add Docker and docker compose support to the Ubuntu's packages list
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-pluginsudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-pluginlinux/ubuntu   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
 
### Install docker and docker compose on Ubuntu
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
 
### Verify the Docker and docker compose install on Ubuntu
sudo docker run hello-world
```

### Docker win or mac

If do not meet the requirements then we use **Docker Toolbox** if we do then **Docker Desktop**

```bash
### Manipulate running Docker machine
docker-machine ls
docker-machine status default

docker-machine stop default
docker-machine start default
docker-machine restart default

### Recreation of Docker machine
docker-machine rm -f default
docker-machine create --driver virtualbox default
docker-machien start default

### Get the host IP address
docker-machine ip default

### Connect to a virtual machine through Docker
docker-machine ssh default

### Copy remote foo.txt to our current directory
docker-machine scp default:/home/docker/foo.txt .

### Installs the latest Docker into our Virtual machine
docker-machine upgrade default
```

![Docker flow](https://raw.githubusercontent.com/matebence/docker/master/docker_flow.png)

### List images, containers, volumes and networks

```bash
docker images
docker ps
docker volume ls
docker network ls
```

### List images, containers, volumes and networks

```bash
docker ps -a
docker images -a
```

### Show last exited container

```bash
docker ps -l
```

### Create container from image

```bash
## Create container from nginx
docker run nginx:latest

## Create container from nginx and run in interactive shell mode
docker run -ti nginx:latest

## Create container from nginx and run in the background
docker run -d nginx:latest

## Create container from nginx and if exits remove the container
docker run --rm nginx:latest

## Create container from nginx with name app
docker run --name app nginx:latest 

## Create container from nginx with env variable
docker run -e ENV=123 nginx:latest

## Running XY linux command, based on CMD and ENTRYPOINT
docker run --name app nginx:latest <XY linux command>
```

### Enter interactive shell for container xy

```bash
docker exec -it container_name bash
```

### Docker networking

```bash
## Networking, get port from container and dynamically map to the host
docker run -p CPORT_DYNAMIC nginx:latest 

## Networking, map the containeter port to the host port
docker run -p HPORT:CPORT nginx:latest 

## Networking, communication via host
docker run -d --add-host=host.docker.internal:host-gateway --name app nginx:latest
	ping host.docker.internal:80
	
## Networking communication over the host
docker run -p 1234:80 --name app nginx:latest 
docker run -it --name client ubuntu:latest
	ping 172.17.0.1:1234

## Networking via link (only one way from client to app)
docker run --name app nginx:latest 
docker run -it --link app --name client ubuntu:latest
	cat /etc/hosts
	ping app:1234

## Networking via private networks
docker network create example
docker run -d --net=example --name app nginx:latest 
docker run -it --net=example --name client ubuntu:latest
	ping app:80
```

### Docker volumes

- Volumes are folder on the host machines hard drive which are mounted into containers
- Volumes persist if a container shuts down. 
- If container re(starts) and mount a volume, any data inside of that volume is avaible in the container
- A container can write data into a volume and read data from it

Two types of external data stores:
- Volumes (stored at /var/lib/docker)
	-Anonymous volumes
		- Docker sets up a folder
		- Data is not persistent
		- Helps us by preventing data override from our Dockerfile during COPY command
		- Removed when container removed
		- -v /app/nodes_modules
		- docker volume ls
	-Named volumes
		- A defined path in the container is mapped to the created volume mount.
		- Great for data which should be persistent but which you dont need to edit (editing on the host machine)
		- Stays persistent even after container remove
		- -v feedback:/app/feedback
		- docker volume ls
- Bind Mounts
	- You define a folder path on your host machine
	- Great for persitent, editable data (editing on the host machine)
	- Stays persistent even after container remove
	- -v /Users/ecneb/dev/node_app:/app

```Dockerfile
FROM node:14
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 80
VOLUME ["app/feedback"]
CMD ["node", "server.js"]
```

```bash
docker run -d -v "feedback:/app/feedback" -v "/Users/ecneb/dev/node_app:/app" -v "/app/node_modules" -p 3000:80 --name feedback-app feedback-node:volumes
```

```bash
## Creating anonymous volume
docker run -d -v "/home/docker" nginx:latest

## Creating named volume
docker run -d -v "testNamedValue:/home/docker" nginx:latest

## Creating bind mount
docker run -d -v "/home/ecneb/dev/app:/home/docker" nginx:latest

## Managing volumes
docker volume ls
docker volume create test
docker volume rm rest
```

### Logs

```bash
## Show logs
docker logs container_name

## Show and watch logs
docker logs -f container_name

## Show and watch logs from last 10 rows
docker logs -f --tail 10 container_name
```

### Managing containers 

```bash
## Start container
docker start container_name

## Attach container (from background to foreground)
docker start -a container_name

## Restart container
docker restart container_name

## Stop and kill running containers
docker stop container_name
docker kill container_name
```

### Managing images

```bash
## Create new image from container (with tagging)
docker commit container_name ecneb/new_image_name:tag

## Retaging an image
docker tag image_name ecneb/new_image_name:tag

## Pushing image to registry
docker push ecneb/new_image_name:tag

## Pushing to custom repository
docker push hub.docker.com/ecneb/new_image_name:tag

## Downloading image from registry
docker pull nginx:latest

## Saving images debian:sid busybox ubuntu:14.04 locally into my-images.tar.gz
docker save -o my-images.tar.gz debian:sid busybox ubuntu:14.04

## Load local images from my-images.tar.gz
docker load -i my-images.tar.gz
```

### Deleting containers and images

```bash
## Remove container
docker rm container_name

## Remove image
docker rmi image_name
```

### Copy files from container

```bash
docker cp container_name:/container_directory ./container_directory
```

### Docker Authentication

```bash
docker login
docker logout
```

### Clearing unused resources

```bash
## Clearing unused images, containers, volumes and networks
docker image prune
docker container prune
docker volume prune
docker network prune
```

### Building Docker Images

- Docker image build has a layared architecture
- Every instruction has its own layer
	- If there is no change everything comes from the cache
	- So its important to seperate changing and non changing items in a Dockerfile

During image build we use two files:
- **.dockerignore** - list files and directories which should be ignored by Docker
- **Dockerfile** - instruction how to build the Docker image

**Most used instructions**

- The **FROM** statement
	- Which image to download and start from

- The **MAINTAINER** statement
	- Defines the authothor of thos Dockerfile

- The **RUN** Statement
	- Runs the command line

- The **ADD** statement
	- Adds local files

- The **ENV** statement
	- Sets environment variables

- The **ARG** statement
	- Sets build-time variables

- The **ENTRYPOINT** and CMD statements
	- ENTRYPOINT specifies the start of the command to run

- The **EXPOSE** Statement
	- Maps a port into the container

- The **VOLUME** statement
	- Defines a shared volumes (anonymous)

- The **WORKDIR** statement
	- Sets the directory the containers start in

- The **USER** statement
	- Sets which user the container will run as

**Simple builds**

```Dockerfile
FROM node:14
MAINTAINER Bence Mate <bence.mate@github.com>
RUN groupadd -r bence && useradd -r -g bence bence
ARG DEFAULT_PORT=80
WORKDIR /app
ADD package.json .
RUN npm install
COPY . .
ENV PORT $DEFAULT_PORT
EXPOSE $PORT
VOLUME ["/app/feedback"]
ENTRYPOINT ["node"]
CMD ["app.mjs"]
USER bence
```

**Multi-Stage Builds**

We build our node project in node:14-alpine and then we copying it to nginx:stable-alpine

```Dockerfile
FROM node:14-alpine as build
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

FROM nginx:stable-alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Building Docker images

```bash
docker build -t ecneb/my-project:v1 --build-arg DEFAULT_PORT=8000 .
```

### Docker Compose - Simple-Container Orchestration

### Kubernetes
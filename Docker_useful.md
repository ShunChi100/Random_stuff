# Docker
Images are frozen immutable snapshots of live containers. Containers are running (or stopped) instances of some image.

Container only lives as long as the process inside it is alive.
## Commands

#### `docker images`
list all images
#### `docker rmi image_name`
remove an image by image name. _Note: must delete all dependant containers first_.

To remove all images `docker rmi $(docker images -q)`

#### `docker pull image_name`
pull an image from docker hub to the host. (not run it)

#### `docker run image_name`
run an image. If the image is not in the local computer, try to pull from docker-hub.

`docker run -d image_name`: run a container in detached mode. The container runs in the backend.

`docker run image_name --name container_name`: specify the container name.

`docker attach container_id/name` to run the docker in the attach mode

`docker run image_name:1.0`: pulls an image of the `1.0` version and run a container. Here `1.0` is a tag

`docker run -i image_name`: run the container in the interactive mode. It allows the user to map the standard input of the host to the docker container

`docker run -it image_name`: run the container and attach the contain to a sudo terminal as well as in an interactive mode. 

`docker run -p 80:5000 image_name`: run the container with external port `80` mapped to `5000`. So this container can be access by `host_ip:80`.

`docker run -p <host_port1>:<container_port1> -p <host_port2>:<container_port2> image_name`: run with multiple ports mapping

`docker run -v /opt/datadir:/var/lib/mysql mysql`: map a directory `/opt/datadir` on the container on the docker host to a directory `/var/lib/mysql` inside the container. In this way, the data will be persisted on the docker host when the docker container is deleted. Two types of mounting:
1. volume mount: `docker run -v data_volume:/var/lib/mysql mysql`, where `data_volume` is created in `docker/volumes` of the Docker Host.
2. bind mount: `docker run -v /data/mysql:/var/lib/mysql mysql`, where `/data/mysql` is a data file in the abosulte path `data`.

`-v` can be replaced by more verbose `--mount` using `key=value`, for example
`docker run --mount type=bine,source=/data/mysql,target=/var/lib/mysql mysql`.

`docker run -e APP_COLOR=green simple-webapp-color`: set the environment varialbe and pass to the container.

#### `docker ps`
list all running containers. With option `docker ps -a`, list all the containers both stopped and running.

#### `docker stop container_name/id`
stop an running image. To stop all runing containers `docker stop $(docker ps -aq)`

#### `docker rm container_name/id`
remove an image permanently from local. To remove all runing containers `docker rm $(docker ps -aq)`

#### `docker inspect container_name/id`
return all details of a container as a json object

#### `docker logs container_name/id`
check the logs of a container, particularly useful for detached container running in the background

#### `docker build Dockerfile -t image_name`
build a image locally using a Dockerfile

To build a docker image from a Dockerfile inside current directory
```
docker build . -t image_name
```

#### `docker push image_name`
push a local image to dockerhub

## Dockerfile
Dockerfile is a text file written in specific format that docker can understand. It is an instruciton argument format `Instrcution Argument` such as `RUN apt-get install python`

To build a docker image from a Dockerfile inside current directory
```
docker build . -t image_name
```


However, it'll fail if there's `ADD` or `COPY` command that depends on relative path. For this case, find the directory where the Dockerfile is, then
```
docker build path_to_dir -t image_name
```

Docker image is a layered architecture, each line in the Dockerfile is a layer. Layers are reused.

#### `FROM`
From a base image. All dockerfile starts `FROM` instruction.

#### `RUN`
run some commands to add layers of functionality of the image.

#### `COPY`
Copy files from local host to docker image


#### `CMD`
command to run, such as `CMD sleep 5`

Two formats:
1. `CMD command param1`
2. `CMD ["command", "param1"]` 

#### `ENTRYPOINT`
the `ENTRYPOINT` instruction is like the `CMD` instruciton. You can specify the program that will be run when the container starts, such as `ENTRYPOINT ["sleep"]`. In this way, the parameter can be specified when run a container by `docker run image_name param1`

In case a default parameter is desired, both `ENTRYPOINT` and `CMD` should be used as the following
```
ENTRYPOINT ["sleep"]
CMD ["5"]
```
If no parameter is specified in `docker run image_name`, the container use `5` as default value. But the default value will be override by `docker run image_name 10`.

It is also possible to override the default `ENTRYPOINT` command by `docker run --entrypoint NewCommand image_name some_parameter_value`

#### `EXPOSE`
The port

#### `WORKDIR`
working directory

## Networking in Docker
When you install docker, it creates three networks automatically:
* bridge, the default network a container gets attached to. It is a private internal network created by docker on the host and containers attached to this network (by default) get an internal IP address
* none, the container are not attached any network and doesn't have any access to the external network or other containers. They run in an isolated Network.
* host
If you would like to associate the container with any other network, you can use the `docker run Ubuntu --network=none/host`

In one Docker Host, all containers can resolve each other with the name of the container. Docker has a built-in DNS server that helps the containers to resolve each other using the container name. Note that the DNS Server always runs at 127.0.0.11. Docker uses network namesapces that creates a separate name space for each container. It then uses vitural Ethernet pairs to connect containers together.

#### `docker network ls`
list all networks

#### `docker network create --driver bridge --subnet 182.18.0.0/16 cunstom-isolated-network`
create a new bridge network

#### Connect from a container to a Service on the host
On Windows, use `host.docker.internal` to replace `localhost`, for example `http://host.docker.internal:8000`

On Linux, add `--network=host` to the `docker run`

## Variables
[Here’s a list of easy takeaways](https://vsupalov.com/docker-arg-env-variable-guide/):

* The `.env` file, is only used during a pre-processing step when working with docker-compose.yml files. Dollar-notation variables like $HI are substituted for values contained in an “.env” named file in the same directory.
* `ARG` is only available during the build of a Docker image (RUN etc), not after the image is created and containers are started from it (ENTRYPOINT, CMD). You can use ARG values to set ENV values to work around that.
* `ENV` values are available to containers, but also RUN-style commands during the Docker build starting with the line where they are introduced.
If you set an environment variable in an intermediate container using bash (RUN export VARI=5 && …) it will not persist in the next command. There’s a way to work around that.
* An `env_file`, is a convenient way to pass many environment variables to a single command in one batch. This should not be confused with a .env file.

Setting ARG and ENV values leaves traces in the Docker image. Don’t use them for secrets which are not meant to stick around (well, you kinda can with multi-stage builds).

#### ENV
To declare an `ENV` value in a Dockerfile:
```
# no default value
ENV foo1=
# with a default value
ENV foo2=/bar2

# ENV values can be used during the build
ADD . $foo2
```

`ENV` can also be passed to docker container during launching
```
docker run --env foo1=/bar1 image_name
```
If the host environment also has the `foo1` being set, then it can be passed by 
```
docker run --env foo1 image_name
```

Alternatively, one can collected all `ENV` variables and their values in a `env_file`, then 
```
docker run --env-file=env_var_file image_name
``` 



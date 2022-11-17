# docker
## Commands
```
docker pull $imageName (download image from repository)
docker run $imageName (pull and run image)
docker run -d $imageName (run in deattached mode)
docker run --name $newName $imageName (run docker container with custom name)
docker run -p $clientPort:$containerPort $imageName (exposes the containerPort in the client, it can be in different ports)
docker run ... -e $ENV_VAR=... (set environment variables)
docker ps (show docker containers running)
docker logs $containerName/id (show container logs)
```

## Docker network
In order to conenct these 2 containers (mongo and mongo-express) I must use an isolated docker network. They can talk to each other using just the container name, because they are in the same network (there is no need to use localhost or any port).
The applications that run outside of this docker network (e.g node) are going to connect from outside or from the host using localhost and the port number.

```
docker network ls (shows docker networks)
docket network create $networkName (creates docket network)
```

## Run docker container using an specific network
```
docker run -p $clientPort:$containerPort --net $networkName $image
```
## Run MySQL

```
docker run -d -p 33066:3306 --name $containerName -e MYSQL_ROOT_PASSWORD=$password mysql
```

### Mysql Iterative mode

```
docker exec -it $containerName mysql -p
```
### Exit iterative mode

Type Ctrl+p then Ctrl+q


## Run mongo and mongo-express

### Create networok
```
docker network create mongo-network
```
### Run mongo container
```
docker run -p 27017:27017 -d -e MONGO_INITDB_ROOT_USERNAME=admin ^
-e MONGO_INITDB_ROOT_PASSWORD=password  ^
--name mongodb  ^
--net mongo-network  ^
mongo 
```

### Run mongo express container
ME_CONFIG_MONGODB_SERVER = Mongo container name
```
docker run -d ^
-p 8081:8081 ^
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin ^
-e ME_CONFIG_MONGODB_ADMINPASSWORD=password ^
-e ME_CONFIG_MONGODB_SERVER=mongodb ^
--net mongo-network ^
mongo-express
```

## Docker compose
Allows you to run multiple containers using just one file, you can write here all the commans in a structured way

1. Version: Docker compose version
2. Services: Containers list
    - Container name:
       - image: Image name
       - ports: 
          - -clientPort:containerPort
          - environment:
            - -ENV...=VALUE   
         
```
version:3
services:
    mongodb:
        image: mongo
        ports: 
            - 27017:27017
        environment:
            - MONGO..._USERNAME=admin
```

Docker compose automatically creates an internal network

## Run docker compose
docker-compose -f $fileName.yaml up

> When you create a container this can be pre-configured once and just once, to change the previous configuration you should delete that container and rerun it using the new configuration

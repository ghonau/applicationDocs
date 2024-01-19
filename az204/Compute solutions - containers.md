# Container

With the container you will package the application with its dependencies, libraries, frameworks that are required.

containers are portable

## Docker

This is a open platform that is used for developing, shipping and running applications

Docker has the ability to package and run an application in a loosely isolated environment called a container

Application will run as a container on the docker.

### Docker hub

Docker hub is a public repository for docker images.

Docker will download the image from the docker hub and then create a container for the image and image will be run in its own isolated container.

you can run the command

_sudo docker images_

to see the images list on the machine

you can either download the image first and then run the docker or just run the docker, if there is no docker image, docker will pull / download the image from the docker hub.

_sudo docker run --name -p <host-port>:<container-port>_
host-port : This is the port on the host machine that you want to map
container-port : This is the port inside the container to which you want to map the host port.

_sudo docker run --name appnginx -p 80:80 -d nginx_ (map host 80 to container 80 port)

this command shows the running containers
_sudo docker ps_

In order to build image of the application on your machine you need to have docker toolset installed

To make it easier to configure we will be using linux VM to build the image of the .net application

docker file will have instructions showing how to build docker image for the docker runtime.

1- We will specify the base image which is the runtime for the application
2- it could be mcr.microsoft.com/dotnet/aspnet:6.0
3- You will need to create WORKDIR /app which is absolute or relative path it will created if does not exists and it will be the destination for your copy command
4- Copy . . will copy everything from the linux server where the the docker file is to the image working directory which is /app
5- EXPOSE 80
6- ENTRYPOINT ["donet", "sqlapp.dll"]

### How to build image

_sudo docker build -t sqlapp ._ (last . means the ""Dockerfile"" is in the current folder).

_sudo docker ps_ shows the container running
_sudo docker stop 9ec_ will stop the running container on the port 80

### How to run created image

_sudo docker run --name sqlapp-1 -p 80:80 -d sqlapp_

_sudo docker stop 3we_ stop the running container
_sudo docker rm 3we_ to remove the container
_sudo docker image rm sqlapp_ will remove the image from docker

### ACR azure container registry

When you have different environment you can use the ACR to have the repository for your images so that your container will download the image from the repository and create the container in the same fashion for all the environments

you can enable admin user on ACR

#### Pushing image to ACR

You will need to use Azure CLI on your linux VM
1- Login to acr
sudo az acr login --name appregistery32342 --username username --password password

2- Tag the image on the VM

_sudo docker tag sqlapp appregistery32342.azure.io/sqlapp_ name of the azure container registry/repository where you want to
This is to create an alias of the image with fully qualified path to your registry

3- Push the tagged image to registry
_sudo docker push appregistery32342.azure.io/sqlapp_ this is to push image to fully qualified private registry

#### Azure Container Instances

You do not need to install docker to run the containers, docker environment is managed for you

1- Deployment : provides a fast and easy way to deploy containers
2- Infrastructure : You do not need to manage underlying infrustructure
3- Public Access: Azure container instances can get their own public IP and DNS name.
4- You can also persist data via the use of Azure File Shares.

#### Multi stage docker file

you will have different layers in your docker file each their own base image
and it will remove the intermediate container as it progresses to minimize the content of the docker image.

#### Azure container group

1- Collection : This is a collection of containers that get scheduled on the same host machine.

2- Resources: The containers share the lifecycle, resources, local network and storage volume.

3- Deployment: the container deployment group is done via a Resource Manager template or a YAML file.

4- File Share: you can also persist data via the use of Azure file share.

During deployment with Azure Container Group you can use the connection string with localhost as all the containers will be deployed on the same host machine.

#### Execure commands

Here is an exmple how to run a mysql command against the container in Linux VM

_sudo docker exec -it mysql-instance mysql -u root -p azure123_

Command to initialize the database with the reserved directory

### Deployment via yaml

you can start azure shell and upload yaml file which has got the deployment to container registry instances commands for database and web app

You can use the following command
az container create --resource-group docker-grp --file deployment.yaml

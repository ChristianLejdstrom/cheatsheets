# Docker
<!--ts-->
* [Docker](docker.md#docker)
* [Base commands](docker.md#base-commands)
   * [pull](docker.md#pull)
   * [images](docker.md#images)
   * [run](docker.md#run)
      * [Options:](docker.md#options)
      * [Example:](docker.md#example)
   * [push](docker.md#push)
   * [exec](docker.md#exec)
* [Docker Container](docker.md#docker-container)
   * [ls](docker.md#ls)
      * [Options:](docker.md#options-1)
   * [stop](docker.md#stop)
   * [rm](docker.md#rm)
   * [attach](docker.md#attach)
   * [port](docker.md#port)
* [Clear cache, remove unused Docker objects](docker.md#clear-cache-remove-unused-docker-objects)
* [Docker Build](docker.md#docker-build)
   * [build from context](docker.md#build-from-context)
   * [build from specified dockerfile](docker.md#build-from-specified-dockerfile)
      * [Powershell](docker.md#powershell)
      * [Bash](docker.md#bash)
   * [Build from context with dockerfile](docker.md#build-from-context-with-dockerfile)
   * [Multistage docker builds](docker.md#multistage-docker-builds)
* [Dockerfile](docker.md#dockerfile)
   * [COPY vs ADD](docker.md#copy-vs-add)
   * [Windows-specific](docker.md#windows-specific)
   * [COPY](docker.md#copy)
   * [Sample Dockerfile](docker.md#sample-dockerfile)
* [Docker Compose](docker.md#docker-compose)
   * [Sample Docker Compose file](docker.md#sample-docker-compose-file)
   * [Docker Compose commands](docker.md#docker-compose-commands)
      * [Run a docker compose file as a service](docker.md#run-a-docker-compose-file-as-a-service)
      * [Run a docker compose file in the foreground](docker.md#run-a-docker-compose-file-in-the-foreground)
      * [List images used by a docker compose file](docker.md#list-images-used-by-a-docker-compose-file)
* [Docker Registry](docker.md#docker-registry)
* [Troubleshooting](docker.md#troubleshooting)
   * [The reference assemblies for .NETFramework,Version=v3.1.411 were not found... (on docker build)](docker.md#the-reference-assemblies-for-netframeworkversionv31411-were-not-found-on-docker-build)
   * [It was not possible to find any installed .NET Core SDKs (on docker run)](docker.md#it-was-not-possible-to-find-any-installed-net-core-sdks-on-docker-run)
   * [The local machine's clock may be out of sync with the server time by more than five minutes](docker.md#the-local-machines-clock-may-be-out-of-sync-with-the-server-time-by-more-than-five-minutes)
   * [Windows server image: The system cannot find the file specified](docker.md#windows-server-image-the-system-cannot-find-the-file-specified)

<!-- Added by: runner, at: Sun Feb  6 08:58:45 UTC 2022 -->

<!--te-->

# Base commands

## pull

Pull an image from a remote repo.

```bash
docker pull <image-url>
```

## images

Lists all available images.

```bash
docker images
```

## run

Run an image in a new container.

```bash
docker run [Options] <image name/image url> <command to run>
```

### Options:

- **-e "ENV_VAR=value"**, pass an environment variable 
- **--name** <string>
- **-i**, interactive
- **-d**, detached
- **-t**, pseudo terminal
- **-P**, expose ports
- **-v**, Bind mount a volume

### Example:
```bash
docker run --name jenkins -p 8080:8080 -p 50000:50000 -v /your/home:/var/jenkins_home 1282bc63ab17
```

## push

Push an after it has been bult

```bash
docker login
docker push <publisher name>/<image name>
```
  
To keep the container alive: 
```bash
docker run -d -t alpine
```
  
## exec

Exec will run a program within a container, a few examples:

```bash
docker exec -i -t graceful_hopper /bin/bash
docker exec -i -t python-test python3
```

# Docker Container

## ls

Lists running containers

```bash
docker container ls
```

### Options:

- **-a**: Lists historial processes as well

## stop

Stops a running containers

```bash
docker container stop <container name> 
```

## rm

Removes/deletes a historical containers

```bash
docker container rm <container name> 
```

## attach

Attaches a shell to a running container, referenced by name

```bash
docker container attach <container name> 
```

## port

Displays exposed ports of a running container

```bash
docker container port <container name>
docker port <container name> 
```

# Clear cache, remove unused Docker objects
Remove all stopped containers, dangling images, and unused networks:
```bash
docker system prune
```
  
# Docker Build

## build from context
```bash
docker build -t test/restapp .
```

## build from specified dockerfile

### Powershell
```bash
Get-Content C:\Temp\MyDockerFile | docker build -t test/imagename -
```

### Bash
```bash
docker build -t test/imagename - < Dockerfile
```
                                             
## Build from context with dockerfile
```bash
docker image build -t secret-scanner/web .
```

**-t**: specifies image name and tag

## Multistage docker builds

```dockerfile
# syntax=docker/dockerfile:1
FROM alpine:latest AS builder
RUN apk --no-cache add build-base

FROM builder AS build1
COPY source1.cpp source.cpp
RUN g++ -o /binary source.cpp

FROM builder AS build2
COPY source2.cpp source.cpp
RUN g++ -o /binary source.cpp
```

# Dockerfile

## COPY vs ADD
Use Copy for files and ADD for folders:
```dockerfile
COPY test1.txt /temp/
ADD azure-devops-migration-tools/src/MigrationTools.ConsoleFull/bin/Release c:/migrator
```


## Windows-specific

## COPY
```dockerfile
COPY test1.txt /temp/
COPY test1.txt c:/temp/
```
                                             
## Sample Dockerfile
Sample Dockerfile for a flask application
```dockerfile
# our base image
FROM alpine:3.5

# Install python and pip
RUN apk add --update py2-pip

# install Python modules needed by the Python app
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt

# copy files required for the app to run
COPY app.py /usr/src/app/
COPY templates/index.html /usr/src/app/templates/

# tell the port number the container should expose
EXPOSE 5000

# run the application
CMD ["python", "/usr/src/app/app.py"]
```

# Docker Compose

## Sample Docker Compose file
```yaml
version: '3'
services:
  ms-sql-server:
    image: mcr.microsoft.com/mssql/server:2017-latest-ubuntu
    environment:
      ACCEPT_EULA: "Y"
      SA_PASSWORD: "Pa55w0rd2019"
      MSSQL_PID: Express
    ports:
      - "1433:1433"
  color-api:
    build: .
    environment:
      DBServer: "ms-sql-server"
    ports:
        - "8080:80"
```

## Docker Compose commands

### Run a docker compose file as a service
```bash
docker-compose start
docker-compose stop
```

### Run a docker compose file in the foreground
```bash
docker-compose up
```

### List images used by a docker compose file
```bash
docker-compose images
```

# Docker Registry

```bash
# Start your registry
docker run -d -p 5000:5000 --name registry registry:2

# Pull (or build) some image from the hub
docker pull ubuntu

# Tag the image so that it points to your registry
docker image tag ubuntu localhost:5000/myfirstimage

# Push it
docker push localhost:5000/myfirstimage

# Pull it back
docker pull localhost:5000/myfirstimage

# Now stop your registry and remove all data
docker container stop registry && docker container rm -v registry
```

# Troubleshooting
## The reference assemblies for .NETFramework,Version=v3.1.411 were not found... (on docker build)
Add the following to you .csproj:
```xml
  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>
```

## It was not possible to find any installed .NET Core SDKs (on docker run)
Make sure the ENTRYPOINT in your Dockerfile is pointing to the correct dll.
                                             
## The local machine's clock may be out of sync with the server time by more than five minutes

Set the correct timezone and sync the clock in the settings of the host machine (the machine that is running the docker engine).

## Windows server image: The system cannot find the file specified
  
Make sure to mount the C:\vol01 volume

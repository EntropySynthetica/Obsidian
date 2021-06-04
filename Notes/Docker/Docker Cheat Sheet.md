---
tags: [Docker, Kubernetes]
title: Docker Cheat Sheet
created: '2020-01-30T20:16:15.511Z'
modified: '2020-09-10T17:13:58.804Z'
---

# Docker Cheat Sheet
Created Thursday 22 June 2017

Nice cheat sheet availble at http://docker.jens-piegsa.com/

### Install docker latest version
`curl -sSL https://get.docker.com/ | sh`

### Search online repository for docker images,
`docker search <image name>`
`docker search cent`

### Download a docker image
`docker pull <image name>`
`docker pull centos`

### Get a subversion of a docker image
`docker pull <imagename:ver>`
`docker pull centos:6.7`

### Show downlaoded docker images
`docker images`


## Containers

Your basic isolated Docker process. Containers are to Virtual Machines as threads are to processes. Or you can think of them as chroots on steroids.

### Lifecycle

`docker create` 		creates a container but does not start it.
`docker rename` 		allows the container to be renamed.
`docker run` 			creates and starts a container in one operation.
`docker rm`			deletes a container.
`docker update` 		updates a container's resource limits.

Normally if you run a container without options it will start and stop immediately, if you want keep it running you can use the command, docker run -td container_id this will use the option -t that will allocate a pseudo-TTY session and -d that will detach automatically the container (run container in background and print container ID).

If you want a transient container, docker run --rm will remove the container after it stops.
If you want to map a directory on the host to a docker container, docker run -v $HOSTDIR:$DOCKERDIR. Also see Volumes.
If you want to remove also the volumes associated with the container, the deletion of the container must include the -v switch like in docker rm -v.
There's also a logging driver available for individual containers in docker 1.10. To run docker with a custom log driver (i.e., to syslog), use docker run --log-driver=syslog.

Another useful option is docker run --name yourname docker_image because when you specify the --name inside the run command this will allow you to start and stop a container by calling it with the name the you specified when you created it.

### Starting and Stopping

`docker start` 		starts a container so it is running.
`docker stop` 		stops a running container.
`docker restart` 	stops and starts a container.
`docker pause` 	pauses a running container, "freezing" it in place.
`docker unpause` 	will unpause a running container.
`docker wait` 		blocks until running container stops.
`docker kill` 		sends a SIGKILL to a running container.
`docker attach` 	will connect to a running container.

### Info

`docker ps` 		hows running containers.
`docker logs` 		gets logs from container. (You can use a custom log driver, but logs is only available for json-file and journald in 1.10).
`docker inspect` 	looks at all the info on a container (including IP address).
`docker events` 	gets events from container.
`docker port` 		shows public facing port of container.
`docker top` 		shows running processes in container.
`docker stats` 		shows containers' resource usage statistics.
`docker diff` 		shows changed files in the container's FS.
`docker ps -a` 		shows running and stopped containers.

`docker stats --all` 	shows a running list of containers.

### Import / Export

`docker cp` 		copies files or folders between a container and the local filesystem.
`docker export` 	turns container filesystem into tarball archive stream to STDOUT.

The cp command can be used to copy files. One specific file can be copied like:

`docker cp foo.txt mycontainer:/foo.txt`
`docker cp mycontainer:/foo.txt foo.txt`

For emphasis, mycontainer is a container ID, not an image ID.

Multiple files contained by the folder src can be copied into the target folder using:

`docker cp src/. mycontainer:/target`
`docker cp mycontainer:/src/. target`

### Executing Commands

`docker exec` 		to execute a command in container.
To enter a running container, attach a new shell process to a running container called foo, use: docker exec -it foo /bin/bash.


## Images

Images are just templates for docker containers.

### Lifecycle

`docker images` 		shows all images.
`docker import` 		creates an image from a tarball.
`docker build` 			creates image from Dockerfile.
`docker commit` 		creates image from a container, pausing it temporarily if it is running.
`docker rmi` 			removes an image.
`docker load`			loads an image from a tar archive as STDIN, including images and tags (as of 0.7).
`docker save` 			saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7).

### Info

`docker history` 		shows history of image.
`docker tag` 			tags an image to a name (local or registry).

### Cleaning up

While you can use the docker rmi command to remove specific images, there's a tool called docker-gc that will clean up images that are no longer used by any containers in a safe manner.

### Load/Save image

### Load an image from file:
`docker load < my_image.tar.gz`

### Save an existing image:
`docker save my_image:my_tag | gzip > my_image.tar.gz`

### Import/Export container

### Import a container as an image from file:
`cat my_container.tar.gz | docker import - my_image:my_tag`

### Export an existing container:
`docker export my_container | gzip > my_container.tar.gz`



### Show running containers 
`docker ps`

### Show all containers
`docker ps -a`

### Delete a docker container
`docker rm <container ID or Name>`

### Delete all containers
`docker rm -f $(docker ps -a -q)`

`docker container prune`

### Delete all images
`docker image prune`

### Clear out Network and System stuff
`docker network prune`

`docker system prune `

### Delete a docker image
`docker rmi <image ID or Name>`

### Fire up an image in interactive mode, give it a unique name, and start bash 
`docker run -it --name Test centos /bin/bash`

or

`docker exec -it <containerID> /bin/bash`

`docker exec -it 913248e059df5871849bcf66c529c2c14213805842369c72a916bc09ccff2e26 /bin/sh -c "[ -e /bin/bash ] && /bin/bash || /bin/sh"`


## Docker Compose Notes

### Get fresh images
`docker-compose pull`

### Start docker-compose file
`docker-compose up -d`

### Check logs of containers
`docker-compose logs -f`

### Stop all running images
`docker-compose stop netbox netbox-worker redis postgres nginx`

### Remove images
`docker-compose rm -f netbox netbox-worker redis postgres nginx`


## Netbox Docker Notes

### Backup netbox database
`docker-compose exec postgres sh -c 'pg_dump -cU $POSTGRES_USER $POSTGRES_DB' | gzip > db_dump.sql.gz`

### Restore Netbox database
`gunzip -c db_dump.sql.gz | docker exec -i $(docker-compose ps -q postgres) sh -c 'psql -U $POSTGRES_USER $POSTGRES_DB'`


### Docker stop and remove all containers

```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

## Working with remote registries

### Log into a remote registry
`docker login <registryuri>:<registry port>`

Example:
`docker login gitlab.example.com:5050`

### Build a container for the remote repo. 
`docker build -t gitlab.example.com:5050/<gitlab username>/<repo name>:<container tag> .`

Example:
`docker build -t gitlab.example.com:5050/erica.zavaleta/mycontainer:master .`

### Push a container to the remote repo.
`docker push gitlab.example.com:5050/<gitlab username>/<repo name>:<container tag>`

Example:
`docker push gitlab.example.com:5050/erica.zavaleta/mycontainer:master`

### Log out of remote registry
`docker logout <registryuri>:<registry port>`

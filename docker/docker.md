# Run and Start Difference
Run: create a new container of an image, and execute the container. You can create N clones of the same image. The command is: ```docker run IMAGE_ID``` and not docker run CONTAINER_ID

Start: Launch a container previously stopped. For example, if you had stopped a database with the command ```docker stop CONTAINER_ID```, you can relaunch the same container with the command docker start CONTAINER_ID, and the data and settings will be the same.
## Running a container with a name tag
```docker run --name "myContainer" dogus/ubuntu ```

# Container and image difference

An instance of an image is called a container. You have an image, which is a set of layers as you describe. If you start this image, you have a running container of this image. You can have many running containers of the same image.

You can see all your images with ```docker images``` whereas you can see your running containers with ```docker ps``` (and you can see all containers with ```docker ps -a```

So a running instance of an image is a container.



# Removing Images and Containers

We can remove all untagged images by combining ```docker rmi```with the recent dangling=true query:

```docker images -q --filter "dangling=true" | xargs docker rmi```

Docker won't be able to remove images that are behind existing containers, so you may have to remove stopped containers with ```docker rm``` first:

```
docker rm `docker ps --no-trunc -aq`
```

These are known pain points with Docker and may be addressed in future releases. However, with a clear understanding of images and containers, these situations can be avoided with a couple of practices:

Always remove a useless, stopped container with ```docker rm [CONTAINER_ID]```.
Always remove the image behind a useless, stopped container with ```docker rmi [IMAGE_ID]```



# exec vs attach



## attach

This command to attach your terminal’s standard input, output, and error (or any combination of the three) to a running container using the container’s ID or name. attach the running container with the following command.

```
docker attach nodeapi
```

## exec

The `docker exec` command runs a new command in a running container. execute the below command after restarting container nodeapi.

```
// start the container
docker start nodeapi
docker exec -it nodeapi /bin/bash // execute the exec command
```

you can exit now and if you list the containers now we can see container is still running.


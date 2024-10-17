# ros2_MinimalDocker
This package provides definitions and examples on how to use docker in sync with ROS2.

## Docker basics

Here you can find a list of basic, although useful, commands to use docker:

```sh
## IMAGES
# list all docker images
docker images
docker ps # same result

# download an image from a repo
docker pull <image_repo:image_tag>

# build a docker image out of a docker file
docker build -f <Dockerfile> -t <image_repo:image_tag> .

# remove an image
docker rmi <image_repo:image_tag> bash

## CONTAINERS
# run an image (creating a container)
docker run -it --name <container_name> <image_repo:image_tag> bash

# run an image in the background
docker run -itd --rm --name <container_name> <image_repo:image_tag> bash # detached

# run an image that self destroys after exiting
docker run -it --rm --name <container_name> <image_repo:image_tag> bash

# attach to a running container
sudo docker exec -it <container_name> bash

# remove a container
docker rm <container_name>

# remove a running container by force
docker rm -f <container_name>



# remove a network
docker network rm <network_name_or_id>


# remove all containers (do be careful)
docker rm -f $(docker ps -aq)

# remove all images (do be careful)
docker rmi -f $(docker images -aq)

# remove all networks (do be careful)
docker network prune -f

# remove all volumes (do be careful)
docker network prune -f
```


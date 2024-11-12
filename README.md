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

# remove all images (do be careful)
docker rmi -f $(docker images -aq)


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

# remove all containers (do be careful)
docker rm -f $(docker ps -aq)
```
## Docker image example

In this repo I provide you with two docker files, one with a basic ROS2 example and one with a complex ROS2 example where we install dependencies and a ROS2 package.

Here you can find the structure of the simple one, which will simply create a docker image with the `demo-nodes-cpp` package installed:

```Dockerfile
# This is an auto generated Dockerfile for ros:ros-base
# generated from docker_images_ros2/create_ros_image.Dockerfile.em
FROM ros:humble-ros-base-jammy

ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8

ENV ROS_DISTRO humble

# install ros2 packages
RUN apt-get update && apt-get install -y \
    ros-humble-rviz2 \
    ros-humble-demo-nodes-cpp && \
    rm -rf /var/lib/apt/lists/*

# RUN chmod +x ros_entrypoint.sh (not working)
COPY ./ros_entrypoint.sh ./
RUN chmod 755 ros_entrypoint.sh
ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]
```

Which will have the following ros_entrypoint which will simply source the environment:

```bash
#!/bin/bash
set +e

# setup ros environment
echo "source /opt/ros/$ROS_DISTRO/setup.bash" >> ~/.bashrc
exec "$@"
```

## Docker compose example

To build it, we will simply use the following command:

```bash
docker build -f Dockerfile_example -t docker_minimal_example:ros_talker_listener .
```

If we want two different images talking with one another, the best way to do it is by using a docker-compose file which will allow us to run multiple containers at the same time and connect them together.

You can execute it by simply running:

```bash
docker compose up
```

Things to be noted:


### Common Device CGroup Rules in Docker

- In the `device_cgroup_rules` section, we can give extra permissions to the doker image:

1. **USB devices (libusb)**
   - `189:* rmw`: Grants access to USB devices. All dynamically allocated USB devices are registered under this major number. Commonly used for devices managed by the `libusb` library.

2. **CDC/ACM devices (serial communication)**
   - `166:* rmw`: Used for CDC (Communications Device Class) and ACM (Abstract Control Model) devices, such as USB modems and USB serial devices (e.g., Arduino boards).

3. **TTY devices (FTDI, serial ports)**
   - `188:* rmw`: Used for TTY devices. FTDI chips and other serial communication devices typically use this.

4. **Video devices (Linux video capture devices)**
   - `81:* rmw`: Used for Linux video devices such as webcams, TV tuners, and video capture cards.

##
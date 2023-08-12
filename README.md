# Docker Virtual Environment

## Introduction

Virtual environment are commanly used in order to avoid unnecessary environment setups as a result of different computers with different software installed. Here docker is adoped as a comprehensive virtual environment that is applicable for every software.

We provide custom images for using ros, matlab, etc. on desktop computers or rpi, and handy bash scripts to control these virtual environment.

## Before you start

Install docker on your computer, please checkout [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

## Usage

In order to use docker, first you have to build an image, and then create a container.

Here two bash scripts provided to simplify the use of docker virtual environment, namely `build_image.sh` for building images and `start_container.sh` for managing containers.

### build_image.sh

This script is used for building a docker image for a container to run on, use this script by

```bash=
./build_image.sh
```

you will be prompted to choose which docker image from dockerfiles in `Dockerfile` to build from.

> Note: The image name will be the same as the `dockerfile`'s name.

If an existing image has the same name as the `dockerfile`'s name and the tag is `latest`, an option will prompt you to choose whether to change the existing image's tag to `older_versionX` and build the new image with tag `latest`.

#### Using bash arguments

The script also support bash arguemnts as a short hand

```bash=
./build_image.sh IMAGE_FILE REPLACE_OPTION
```

where `IMAGE_FILE` is the dockerfile name in directory `Dockerfile` and `REPLACE_OPTION`(y/n) for controlling whether to change the tag of an image with same name to older vison.

#### Choosing which image to build

The following are provided images:
- fun_time_with_arthur: image with ros and machine learning libraries
- ros_host: image with ros for desktop use
- ros_matlab: image with ros and matlabfor desktop use
- ros_rpi: image with ros for use on raspberry pi

For more information, please refer to `Image environment` in latter section.

### start_container.sh

This script is used for managing a docker container, use this script by

```bash=
./start_container.sh
```

you will be prompted by different commands to manage docker containers:

#### create

This command create a container from existing images.

> Note the host of the container will be the container's name and password in the container for default user `docker` is `docker`.

> Note: In order to let the container be connectable to the interent, port `8080` with host ip `127.0.1.1` is designated for the container, so you can only run a container at a time that was created by `start_container.sh` script.

#### run

This command run a stopped container.

#### shell

This command attach to the shell of a container, if the container is not running, the script will run it first.

> Note: If you want to exit container's shell, simply use `exit` command.

#### stop

This stop a running container.

#### Using bash arguments


The script also support bash arguments as a short hand

```bash=
./start_container.sh COMMAND CONTAINER_NAME IMAGE_NAME
```

where `COMMAND` is the command listed above, `CONTAINER_NAME` is the container you want to manage, and `IMAGE_NAME` is for building container with specific image name.

#### Specific changes for deploying to rpi

When creating a container from `ros_rpi` or `ros2_rpi` images, there will be some sepcific changes:

1. The network of the container and the host are not separated
2. The default user of the container is root

In another word, creating and using this container is as if you are always root on the host, which is **very dangerious**, so please be careful!

### ROS enviroment

There will be ros workspace directory preconfigured in `/home/docker/ws` and its subdirectory `src` mounted to the host as `./packages/CONTAINER_NAME`.

The workspace `~/ws` has already been build(catkin_make) once, so there will also be `build` and `devel` directory beside the `src`.

### ROS1

The general ros environment setup

```bash=
source /opt/ros/noetic/setup.bash
source ~/ws/devel/setup.bash
```

has already being included in `~/.bashrc` file for `ros_host`, `ros_rpi` and `ros_matlab` images, so there is no need to source them everytime.

### ROS2

The general ros2 environment setup

```bash=
source /opt/ros/humble/setup.bash
source ~/ws/install/setup.bash
```

has already being included in `~/.bashrc` file for `ros2_host` and `ros2_rpi` images, so there is no need to source them everytime.

### Support for using nvidia gpu in docker

The script `start_container.sh` support for using gpu inside docker container when creating container, and will automatically create containers if your docker have nvidia docker support. Checkout how to setup nvidia docker support: [How to Use an NVIDIA GPU with Docker Containers](https://www.howtogeek.com/devops/how-to-use-an-nvidia-gpu-with-docker-containers/).

## Known Issues

1. We have some issues when building on WSL. Such as using byobu and visualize environments.
2. Inorder to use matlab, you first have to have the license and the license for matlab cloud use. Campus-wide licenses are already configured for cloud use, if your authorization failed with error code `4402`, please contact your license provider.
3. If it is on **Debian**, follow the steps to solve libseccomp:
  
    ```bash=
    #download from https://packages.debian.org/sid/libseccomp2, for example: 
    wget http://ftp.tw.debian.org/debian/pool/main/libs/libseccomp/libseccomp2_2.5.3-2_armhf.deb
    sudo dpkg -i libseccomp2_2.5.3-2_armhf.deb
    ref: https://askubuntu.com/questions/1263284/apt-update-throws-signature-error-in-ubuntu-20-04-container-on-arm
    ```

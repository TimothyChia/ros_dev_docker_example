# ros_dev_docker_example
A small template or example to show how to create reproducible environments for working with others on ROS code.

## Big Idea

- the main concept here is to imitate the experience of working with ROS on your native machine, but with a few key benefits 
  - benefits
    - easily sharing your environment, so any code you write can be deployed by others
    - troubleshooting when things randomly break. For example, installing one new package may cause all other packages to fail compilation. You never know.
  - 'native' 
    - this means WSLG or Ubuntu, with ROS installed with a standard 'apt install' style
  - 'environment'
    - everything about your system. Ubuntu version, libraries installed, compiler version, laptop microprocessor architecture, ROS version, OpenCV version, python version, PATH variables, environment variables, etc....
- The idea is to systematically install most dependencies in the Dockerfile
  - docker compose is used to ensure the Docker container has access to this entire folder structure, including the catkin_ws
  - You then open a terminal inside the container when you want to edit code or catkin_make
  - In fact, you can and should directly attach VS Code to your docker container

## Folder Structure

- catkin_ws
  - a standard catkin workspace
  - download ROS packages you want to compile into catkin_ws/src
  - follow any other ROS tutorials and instructions needed to make this work, but do it from inside the docker container
- .gitignore
  - in this repo, initialized with github's default .gitignore for ROS
- docker_ros
  - an example Docker setup that was used for another repository
  - this will probably have tons of instructions and dependencies that you do not need for your particular project
  - and will be missing dependencies that you do need
  - note that this Dockerfile uses 
     ``` FROM osrf/ros:noetic-desktop-full ```
    - Which means that it will be Ubuntu 20.04 and ROS Noetic inside.
    - You can use a different base if you require different OS or ROS

## Quick Start
- prepare your environment. 
  - from the docker_ros folder, run build_script.zsh
- download dependencies
- build
  ```
  docker-compose up -d
  docker exec -it docker_ros_dev_1 zsh
  catkin_make
  ```
- place rosbags in a ./datasets folder

## Environment

- Docker Quickstart
    - from the 'docker_ros' directory, build an image into a usable container for development
    - while you can do a simple ``` docker-compose up -d``` , using build_script.zsh will help prevent ownership problems when mounting this repo into the container.
    - 
    - Open terminals in the container
      - ```docker exec -it docker_ros_dev_1 zsh```
      - either attach from VS-Code using the Containers extension to 'attach to a running container'
- More Info
  - see the Dockerfile and docker-compose.yaml for more info about the image and mounting strategy
- Known Issues
    -  Not tested with docker-desktop on windows.
    -  Primarily tested with docker installed in WSL or Linux.
    -  docker-desktop 
       -  previously did not support 'network_mode = host' . Not sure if they've updated to add support yet. 
       -  you can always adjust the docker-compose file if you really need to use docker-desktop. 
       -  however, you may not be able to do things like run gpu-accelerated-rviz on your host machine to visualize output from ros nodes running in the container

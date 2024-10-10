# ROSwithDocker
This is the instruction for ROS installation with the docker container and docker image in VScode.


For different system isolation, I recommend using the docker environment to install the ROS system.

1. Prepare the docker file
   create a new folder name .devcontainer
   dockerfile
   ```
   # Start from a base image (like Ubuntu)
    FROM ubuntu:20.04
    
    
    # Install dependencies
    RUN apt-get update && apt-get install -y \
        curl \
        gnupg2 \
        lsb-release
    
    # Add the ROS repository to the sources list
    RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
    
    # Add the ROS GPG key
    RUN curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -
    
    # Update the package index
    RUN apt-get update
    
    # Install a specific ROS package (optional)
    # RUN apt-get install -y ros-noetic-desktop-full
    
    # Source the ROS setup script (optional step to set up environment for containers)
    RUN echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
    
    # Set the default command (optional)
    CMD ["/bin/bash"]
   ```
   docker-compose.yml
   ```
   version:  "3.8"
    services: 
      devcontainer:
        build: .
        image: my_ros_image
        container_name: ros
        volumes:
          - ~/.devcontainer:/root/.devcontainer
          - ~/code:/root/code
          - ~data:/root/data
          - ~/.ssh:/root/.ssh
          - ~/.gitconfig:/root/.gitconfig
        # command: nvidia-smi
        deploy:
          resources:
            reservations:
              devices:
                - driver: nvidia
                  count: 1
                  capabilities: [gpu]
        entrypoint: bash
        stdin_open: true
        tty: true
   ```
  devcontainer.json
   ```
    {
      "name": "ros",
      "dockerComposeFile": "docker-compose.yml",
      "runServices": ["devcontainer"],
      "service": "devcontainer",
      "workspaceFolder": "/root",
      "context": "..",
    
    "customizations": {
        "vscode": {
            "extensions": [
                "ms-python.python",
                "esbenp.prettier-vscode",
                "eamodio.gitlens"
            ]
        }
    },

    "settings": {
        "terminal.integrated.shell.linux": "/bin/bash"
    },

    "postCreateCommand": "apt-get update && apt-get install -y ros-noetic-rviz ros-noetic-gazebo-ros-pkgs",
    "remoteUser": "root",
    "shutdownAction": "none",

    "runArgs": [
        "--env", "DISPLAY=${env:DISPLAY}",
        "--volume", "/tmp/.X11-unix:/tmp/.X11-unix"
    ]
}

   ```
2. in bash
Desktop-Full Install: (Recommended) : Everything in Desktop plus 2D/3D simulators and 2D/3D perception packages
```
apt install ros-noetic-desktop-full
```
if failed
```
ps aux | grep apt
kill -9 720
dpkg --configure -a
apt install ros-noetic-desktop-full
```
3. catkin
Catkin is the official build system for ROS (Robot Operating System). It is responsible for building, compiling, and managing the dependencies of ROS packages. It replaces the older build system in ROS, called rosbuild, and is designed to work with CMake to handle the building process.

after catkin_make or catkin_build
Source your workspace: After building a workspace, you need to "source" the workspace to update your environment so that ROS knows about the packages you've built. This is done by running:
```
source ~/catkin_ws/devel/setup.bash
```

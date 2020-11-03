# Ubuntu Desktop 16.04 Docker container image with VNC

Ubuntu 16.04 container running the XFCE window manager with GPU support
(via [nvidia-docker](https://github.com/NVIDIA/nvidia-docker)).

The Docker image is installed with the following components:

> - Nvidia driver 410.93
> - CUDA 9.2
> - cudnn 7
> - Python 3.5
> - Python 2.7
> - [Miniconda 2](https://docs.conda.io/en/latest/miniconda.html#linux-installers)
> - [CoppeliaSim Edu](https://www.coppeliarobotics.com/downloads.html)
> - [Mujoco 2.0](https://github.com/openai/mujoco-py)
> - Desktop environment [Xfce4](http://www.xfce.org)
> - [VNC-Server](https://linux.die.net/man/1/vncserver)
> - [noVNC](https://github.com/kanaka/noVNC) - HTML5 VNC client
> - Browser: Mozilla Firefox

Others:
> - The home ($HOME) folder located on: `/headless`
> - Default resolution: `1600x900`
> - Default VNC port: `5901`
> - Default http port (noVNC port): `6901`


##### Tested: torch==1.5+cu92, torchvision==0.6.0+cu9.2 

### Prerequisites:

> - Your computer should have at least one Nvidia video card and installed proper driver (higher 
than 384.81 version) for it.
> - Install [Docker CE](https://docs.docker.com/engine/install/ubuntu/)
> - Install [nvidia-docker](https://github.com/NVIDIA/nvidia-docker)

### Build image
Build the docker container via:

    docker build -t aim_rl .

### Pull image (Recommend)
You can pull image from our [Dockerhub](https://hub.docker.com/repository/docker/tungluu2203/aim_rl). 
Note that, this image is setup with additional packages (Mujoco, RLBench, OpenAI gym, required 
libraries, etc.), that might be different from Dockerfile. For *clean* image (recommend): 

    docker pull tungluu2203/aim_rl:clean

For image with *workspace* (not recommend):

    docker pull tungluu2203/aim_rl:with-env

## Usage
The vnc-server is exposed on port `5901` and vnc web access port `6901`. The
option ```--net=host``` is used below, alternatively use
```-p 5901:5901 -p 6901:6901```. To create a container from this image without mounting:

    nvidia-docker run -d  -t --net=host --name CONTAINER --shm-size 256G tungluu2203/aim_rl:clean

To mount a directory from host into a directory inside container:
    
    nvidia-docker run -d  -t --net=host --shm-size 256G --name CONTAINER \
        --mount type=bind,source=/path/from/host,target=/path/in/container tungluu2203/aim_rl:clean
    
For example, we want to mount `$HOME/workspace` from local host into `/headless/workspace` (Note: 
you should not mount into `/headless`, since you might not source the original `.bashrc` file, it can 
ignore all default configuration):
     
    nvidia-docker run -d  -t --net=host --shm-size 256G --name CONTAINER \
        --mount type=bind,source=$HOME/workspace,target=/headless/workspace tungluu2203/aim_rl:clean
    
After a container is launch, you can connect it via VNC or directly login into the container by:

    docker exec -it CONTAINER bash

To stop the container when done using it run:

    docker stop CONTAINER && docker rm CONTAINER

When a directory is mounted into the container the xfce4 desktop settings
will persist when the container is restarted. 

## Connect & Control

Download a VNC client for your system from
[RealVnc](https://www.realvnc.com/download/viewer/), or use a web-browser to connect. If the 
container is started like mentioned above, connect via one of these options:

> * connect via __VNC viewer `server_ip:5901`__, default password: `vncpassword`
> * connect via __noVNC HTML5 client__: [http://server_ip:6901](), enter password

## Tips

The vnc password, resolution, depth can be changed after the container is launched by editing 
`.bashrc` file.

##### 1) Example: Change the VNC password
After a container is created, modify variable `VNC_PW` in ` `.bashrc` file. 

##### 2) Example: Change the VNC resolution
After a container is created, modify variable `VNC_RESOLUTION` in `.bashrc` file.


## Credits
##### The Dockerfile is based on this [docker-headless-vnc-container](https://github.com/avolkov1/docker-headless-vnc-container)
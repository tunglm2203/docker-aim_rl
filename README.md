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
Note that, this image is setup with additional packages (Mujoco, RLBench, OpenAI gym,  required 
libraries), that  may be different from Dockerfile, for clean image: 

    docker pull tungluu2203/aim_rl:clean

For image with workspace (some RL packages in workspace):

    docker pull tungluu2203/aim_rl:with-env

## Usage
The vnc-server is exposed on port `5901` and vnc web access port `6901`. The
option ```--net=host``` is used below, alternatively use
```-p 5901:5901 -p 6901:6901```. It is possible to use the container as root,
which is highly discouraged.

    nvidia-docker run -d  -t --net=host --name container_name aim_rl   # as root DON'T DO THIS

Running as root is not recommended. A better approach is to run as a user with
home directory mounted.

    TDB

To stop the container when done using it run:

    docker stop container_name && docker rm container_name

When the home directory is mounted into the container the xfce4 desktop settings
will persist when the container is restarted.

## Connect & Control

Download a VNC client for your system from
[RealVnc](https://www.realvnc.com/download/viewer/), or use a web-browser to connect. If the 
container is started like mentioned above, connect via one of these options:

> * connect via __VNC viewer `server_ip:5901`__, default password: `vncpassword`
> * connect via __noVNC HTML5 client__: [http://server_ip:6901](), enter password

## Tips

### Override VNC environment variables
The following VNC environment variables can be overwritten at the `docker run`
phase to customize your desktop environment inside the container:
* `VNC_COL_DEPTH`, default: `24`
* `VNC_RESOLUTION`, default: `1600x900`
* `VNC_PW`, default: `vncpassword`

The resolution can be changed after the container is launched via xrandr as well.

#### 1) Example: Override the VNC password
Overwrite the value of the environment variable `VNC_PW`. For example in
the docker run command:

    nvidia-docker run -d -t --name=container_name --net=host \
      -u $(id -u):$(id -g) -e HOME=$HOME -e USER=$USER -v $HOME:$HOME \
      -e VNC_PW=my-pw \
      -w $HOME aim_rl

#### 2) Example: Override the VNC resolution
Overwrite the value of the environment variable `VNC_RESOLUTION`. For
example in the docker run command:

    nvidia-docker run -d -t --name=mydesk --net=host \
      -u $(id -u):$(id -g) -e HOME=$HOME -e USER=$USER -v $HOME:$HOME \
      -e VNC_RESOLUTION=800x600 \
      -w $HOME ubuntu-vnc-xfce4

Or use xrandr once inside the container VNC session.

## Credits
##### The Dockerfile is based on this [docker-headless-vnc-container](https://github.com/avolkov1/docker-headless-vnc-container)
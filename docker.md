## How to set up server using Docker  

## 0. What is iocker?  

Docker Provide OS-level virtualization.  
The main concept of Docker consists of images and containers, and when images are runed, they become containers.  
Multiple containers can be run and the changed environment is deleted when container is terminated.  
Therefore, the image must be updated through commit before termination.  
The image is not entirely updated, but only a few are updated and are added based on the base image.  
Therefore, files that you use directly must be stored in a shared mounted directory.  

## 1. Install GPU driver for your PC  

Put below command on your bash shell then you can find recommanded driver for your PC.  

`$ ubuntu-drivers devices`  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res01.PNG?raw=true)  

Install GPU driver.  

~~~
// Update repository.  
$ sudo add-apt-repository ppa:graphics-drivers/ppa  
$ sudo apt update  

// Check recommeded driver can be used.  
$ apt-cache search nvidia | grep nvidia-driver-460  
~~~  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res00.PNG?raw=true)  
  
**- Using APT(select 1 or 2)**   
~~~
// 1. Install driver by apt.  
$ sudo apt-get install nvidia-driver-460  

// 2. Using auto install.  
$ sudo ubuntu-drivers autoinstall  

// Reboot.  
$ sudo reboot  
~~~  
※ If some error occured, remove Nvidia related package and configure file.  
`$ sudo apt --purge autoremove nvidia*`  

**- Using software & updates.**  
![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res000.png?raw=true)  
![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res001.png?raw=true)  

**- Using download drivers.**  

Go to NVIDIA driver download [page](https://www.nvidia.com/Download/index.aspx).  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res011e.png?raw=true)  
![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res012e.png?raw=true)  

And follow below insruction.  
~~~  
// before do this, keep download path.  
// Deactivate DisplayManager.(Display will be downed and enter to CUI mode. so keep this page in different device.)    
$ systemctl isolate multi-user.target  

// Remove Nvidia related package and configure file.  
$ sudo apt --purge autoremove nvidia*  

// Add permission to download file.  
$ chmod +x <file_path>/<file_name>.run  

// install.  
$ sudo sh <file_path>/<file_name>.run  

// Reactivate DisplayManager.  
$ systemctl start graphical.target  
~~~

Next, Check gpu driver is working.  

`$ nvidia-smi`  
  
![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res02.PNG?raw=true)  

## 2. Install Docker.  

In your ubuntu, only docker package is installed, and other packages like tensorflow, numpy, matplotlib.. is installed on a container and saved as image.  
You can download popular images on [`Docker hub`](https://hub.docker.com/), But in here, we download base images and install package ourself.(and commit to image)  

To use GPUs in a docker, you need to use an [nvidia-docker](https://github.com/NVIDIA/nvidia-docker#quickstart).  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res06.png?raw=true)  

Official image in nvidia-docker git repository.  

**- Install Docker**  
To use nvidia-docker, Nvidia-driver and docker should be installed on OS.  
First, download docker.  

Go to [here](https://docs.docker.com/get-docker/) and download docker and follow the istruction.  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res07.PNG?raw=true)  

And check the version using `$ sudo docker version`  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res08.PNG?raw=true)  


Actually Docker always need root permission so give root permission for Docker(we can skip `sudo` command.).  
Check [here](https://docs.docker.com/engine/install/linux-postinstall/).  

~~~  
// create group docker.  
$ sudo groupadd docker  

// add user to this group.  
$ sudo usermod -a -G docker $USER

// logout and log back in, check state.  
# docker run hello-world  
~~~  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res09.PNG?raw=true)  

Below commands are useful for manage Docker.  
Before setup remind below commands.  

#### commands.  

|command|operation|example|  
|--|--|--| 
|`$ docker version`|show version| - |   
|`$ docker search <keyword>`|find images on Docker Hub.|`$ docker search ubuntu`|  
|`$ doker images`| show docker image list. | - |  
|`$ docker push <DockerHub_user_id>/<Image_name>:<tag>`| push docker images to docker hub.| `$ docker push jonghyeok/ubuntu:11.11`|  
|`$ docker pull <image_name>:<tag>`| pull docker images form docker hub.| `$ docker pull ubuntu:latest`<br>`$ docker pull ubuntu:18.04`|  
|`$ docker run <option> <image_name> <run_command>`|run container.<br>if not exist, download from DockerHub.|`$ docker run -i -t ubuntu /bin/bash`|  
|`$ docker ps`|print current running containers state.| - |  
|`$ docker attach <container_name>`|connect to container which process runs inside.(keep process like jupyer-notebook.) |`$ docker attach ubuntu`|  
|`$ docker commit <container_name> <image_name>:<tag>`| commit changes on container to image.| `$ docker commit ubuntu_my ubuntu:18.04` |  
|`$ docker restart <container_name>`| restart container. |`$ docker restart ubuntu_my`|  
|`$ docker stop <container_name>`|stop container.|`$ docker stop ubuntu_my`|  
|`$ docker exec <container_name or container_id> <command> <params>`|run bash command at outside of container.(container should be run)|`$ docker exec ubuntu_my apt-get update`|  
|`$ docker rm <container_name>`| remove container.|`$ docker rm ubuntu_my`|  
|`$ docker rmi <image_name>:<tag>`|remove image.|`$ docker rmi ubuntu:18.04`|  
|`$ docker tag <prev_image_name>:<tag> <new_image_name>:<tag>`|change image name.|`docker tag nvidia/cuda:10.0-base numpy_nvidia/cuda:10.0-base`|  


#### docker run options.  

|options|operation|example|  
|--|--|--|  
|`-i -t` or `-it`| interactive and Pseudo-tty(virtual terminal), you can use bash on command line. | `$ docker run -it ubuntu /bin/bash`|  
|`-e <varaible>`| set environment variable.|`docker run -e NVIDIA_VISIBLE_DEVICES=1 tensorflow/tensorflow`|  
|`-p <host>:<container>`|open port on container.([for jupyter, tensorboard](https://www.tensorflow.org/tensorboard/tensorboard_in_notebooks))|`$ docker run -it -p 8888:8888 -p 6006:6006 tensorflow/tensorflow`|  
|`-v <host_dir>:<container_dir>`|make volume in host.|`docker run -v ${HOME}/data:/home/jonghyeok/data ubuntu /bin/bash`|  
|`--name <name>`| set container name. | `$ docker run -it --name my_name ubuntu /bin/bash`|  
|`--rm`|when container terminated, remove file system. |`docker run -ti --rm -e NVIDIA_VISIBLE_DEVICES=1 --runtime=nvidia nvidia/cuda`|  
|`--runtime=<runtime>`| change the runtime.<br>if set nvidia, can use CUDA Toolkit.|`docker run -ti --rm -e NVIDIA_VISIBLE_DEVICES=1 --runtime=nvidia nvidia/cuda`|  

#### nvidia-docker run options. ([guide](https://docs.nvidia.com/deeplearning/frameworks/user-guide/index.html))
|`-d`| Docker to run in daemon mode; no tty, run in background (not shown in the command) |`nvidia-docker run -d --shm-size 6G -it --name my_name nvidia/cuda:10.1-base /bin/bash`|  
|--|--|--|  
|`--shm-size`|This line is a temporary workaround for a DIGITS multi-GPU error you might encounter. |`nvidia-docker run -d --shm-size 6G -it --name my_name nvidia/cuda:10.1-base /bin/bash`|  


Install [nvidia-docker2](https://github.com/NVIDIA/nvidia-docker)
Go to [here](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#installing-on-ubuntu-and-debian) and follow instructions.  


Now, run images!    
First, Download images from DockerHub.  before that, you should check your environments [here](https://github.com/NVIDIA/nvidia-docker/wiki/CUDA#description).  

We should check what image is matched with our server. You can check [here](https://hub.docker.com/r/nvidia/cuda).  

In mycase will use cuda:10.0,  

`$ docker run -it -v ${HOME}/data:/home/jonghyeok/data nvidia/cuda:10.0-base /bin/bash`  

And give permission to `/home/jonghyeok/data`.  
`$ sudo chmod -R 777 /home/jonghyeok/data`  

If image is not in local, it will be downloaded.  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res13.PNG?raw=true)  

You can see bash is changed. I just open new session and check `$ docker ps`.  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res14.PNG?raw=true)  

## 3. Install some packages and commit to images.  
We install python 3.6 here.  

~~~  
# apt-get update  
# apt-get install -y software-properties-common  

// latest version should use 'ppa:deadsnakes/ppa'
# add-apt-repository -y ppa:fkrull/deadsnakes  

# apt-get update  
# apt-get install -y --no-install-recommends python3.6 python3.6-dev python3-pip python3-setuptools python3-wheel gcc  

//install git also  
# apt-get install -y git  
~~~  

Just install python packages using `$ pip3 <package>`.  
Let's install numpy here.  
First check numpy is installed.  

`$ pip3 list | grep numpy`  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res15.PNG?raw=true)  

Press (Ctrl + P) + (Ctrl + Q) to exit the container.(Container will not shut down, you should put `-it` options when run.)  
And check container state using `$ docker ps`.  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res16.PNG?raw=true)  

Commit changes,  
`$ docker commit epic_chaplygin nvidia/cuda:10.0-base`  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res17.PNG?raw=true)  


For test, stop container,  
`$ docker stop epic_chaplygin`  

Change image name for identify,  
`$ docker tag nvidia/cuda:10.0-base numpy_nvidia/cuda:10.0-base`  

Run aganin and test numpy,  
`$ docker run -it -v ${HOME}/data:/home/jonghyeok/data numpy_nvidia/cuda:10.0-base /bin/bash`  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res18.PNG?raw=true)  

Done! And more useful tips [here](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/usable.md)!  


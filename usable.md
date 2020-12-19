## Usable Things.

#### .bashrc  

It is placed at `/home/<user_name>/.bashrc` (It only work for specific user.)  
When bash start, this setting is applied.  

※ You shoud set Cuda Eenviroment variaeble here.  

#### Stop script. 
- ctrl + C : You can get out of state.  

#### File Edit.  
Many times you can see below command.  

`$ vi ~/text.txt`  
  
It is difficult for anyone unfamiliar with the vi editor to modify the text.  
So use below command, using gedit or nano.  

~~~
$ gedit ~/text.txt   //using gedit.
$ nano ~/text.txt   //using nano.
~~~  

#### Dependancy problem.  
In my case when install gpu driver below error occured.  
`The following packages have unmet dependencies:~~`  

So i just follow [this](https://askubuntu.com/questions/563178/the-following-packages-have-unmet-dependencies) solution.    

~~~  
$ sudo apt-get install aptitude
$ sudo aptitude install nvidia-driver-460
~~~  

Next reply to replace driver like below (my case N, Y, Y {change the driver.}).  
~~~
The following actions will resolve these dependencies:

Keep the following packages at their current version:
1)     ~~~~~~~                        

Accept this solution? [Y/n/q/?] n

The following actions will resolve these dependencies:

Downgrade the following packages:                                
1)     ~~~~~ ubuntu2.3 (now) -> ubuntu2 

Accept this solution? [Y/n/q/?] Y

...
~~~  


## Docker  
In official website.  
The contents of /var/lib/docker/, including images, containers, volumes, and networks, are preserved. If you do not need to save your existing data, and want to start with a clean installation, refer to the [uninstall Docker Engine section](https://docs.docker.com/engine/install/ubuntu/#uninstall-docker-engine) at the bottom of this page.  


#### - Clean images.  
  If you continue to use the dockers, images that you don't use accumulate. So the images you don't need should be cleaned up for storage, so the functions below help you.  

  ~~~  
  // Clean all unused or unused images.  
  $ docker image prune  

  // not used which one run as container will be cleaned.  
  $ docker image prune -a  
  ~~~  

#### - export and import containers and images.  
  
  ~~~  
  // save image.  
  $ // docker save <option> <file_name>.tar <image_name>:<tag>  
  $ docker save -o /home/jonghyeok/data/jonghyeok.tar numpy_nvidia/cuda:10.0-base  
  
  // load image.
  $ // docker load < <file_name>.tar  
  $ docker load < /home/jonghyeok/data/jonghyeok.tar  

  // save container.  
  $ docker export <container_name> > <file_name>.tar  

  // load container.  
  $ docker import <file_name>.tar  
  ~~~  

#### -  Remove remained docker container space.  
   If you stop the container which run without `-rm` options, it wii be automatically removed.  
   But this container still occupy the storage space.  

   Check, 
   `$ docker ps -a`  

   So if you use below, you can remove all.  
   `$ docker ps -a | grep Exit | cut -d ' ' -f 1 | xargs sudo docker rm`  

   Check,
   `$ docker ps -a`  


## CUDA.  
- Check version  

~~~  
// check cuda version.  
$ cat /usr/local/cuda/version.txt  
//or  
$ nvcc  --version  

// check cudnn version.  
$ cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2  
//or
$ cat /usr/include/cudnn.h | grep CUDNN_MAJOR -A 2  
~~~  
  

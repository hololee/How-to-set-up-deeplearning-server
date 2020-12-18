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

  
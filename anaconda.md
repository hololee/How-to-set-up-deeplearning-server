# How to set up server using Anaconda  

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

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res011.png?raw=true)  
![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res012.png?raw=true)  

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

## 2. Install CUDA® Toolkit  
  
For using Tensorflow or pytorch on GPU, We need to install CUDA® Toolkit and cuDNN.  
There is a supported version relationship between Gpu driver, CUDA® Toolkit, and cuDNN, so be sure to check. ([Check here](https://www.tensorflow.org/install/gpu))  

I'll Install CUDA 10.1 here.  

- Go to [This Page](https://developer.nvidia.com/cuda-toolkit-archive) and follow the instruction.  
  
![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res03.PNG?raw=true)  

~~~
$ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
$ sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
$ wget https://developer.download.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda-repo-ubuntu1804-10-1-local-10.1.243-418.87.00_1.0-1_amd64.deb
$ sudo dpkg -i cuda-repo-ubuntu1804-10-1-local-10.1.243-418.87.00_1.0-1_amd64.deb
$ sudo apt-key add /var/cuda-repo-10-1-local-10.1.243-418.87.00/7fa2af80.pub
$ sudo apt-get update
$ sudo apt-get -y install cuda
~~~  

Next, set Environment variabels on .bashrc.  

`$ gedit ~/.bashrc`  

Add below.  

~~~
export PATH=/usr/local/cuda-10.1/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-10.1/lib64:${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
~~~  

※ You should change `cuda-10.1` to yours, you can find directory name here `/usr/local/`.  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res04.PNG?raw=true)  

Finally, check CUDA version.  

`$ nvcc --version`  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res03-1.PNG?raw=true)  

## 3. Install cuDNN  

[Download](https://developer.nvidia.com/cudnn) cuDNN(You need to Nvidia account.).  
  
I'm going to install cuDNN@7.6 here.  
Click 'Download cuDNN Runtime Library for Ubuntu18.04 (Deb)'.  
※ Developer Library contains examples for test.  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res05.PNG?raw=true)  
  
Run below command.  

~~~
$ cd <downloaded_directory>  
$ sudo dpkg -i <file_name>.deb  
~~~

Next, check cuDNN version.  

`$ cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2`  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res05-1.PNG?raw=true)  

## Install Anaconda  

First. install Anaconda [here](https://www.anaconda.com/products/individual#download-section).  

Create conda env(In this case, I istalled python 3.6 ver).  

`$ conda create -n <env_name> python=3.6`  

Below commans usable commands.  

~~~
// Create env by name and version.  
$ conda create -n <env_name> python=x.x  

// Activate env. 
$ source activate <env_name>  
~~~  

Activate env and install tensorflow, pytorch or etc.  
※ The install and test code is referenced in the following [blog](https://teddylee777.github.io/linux/%EB%94%A5%EB%9F%AC%EB%8B%9D-PC%EC%97%90-ubuntu%EC%99%80-CUDA-GPU%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0).  

Install tensorflow and test.  

~~~
// install tensorflow gpu ver.     
$ pip install tensorflow-gpu  

// run python  
$ python  

// test  
>>> import tensorflow as tf  
>>> tf.test.is_gpu_available(  
... cuda_only=False,  
... min_cuda_compute_capability=None  
... )  
~~~  

Install pytorch and test.  

~~~  
// install pytorch.  
$ conda install pytorch torchvision cudatoolkit=10.1 -c pytorch  

// run python  
$ python  

// test  
>>> import torch  
>>> torch.cuda.is_available()  
~~~

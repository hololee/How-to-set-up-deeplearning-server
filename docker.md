## How to set up server using Docker  

### 1. Install GPU driver for your PC  

Put below command on your bash shell then you can find recommanded driver for your PC.  

`$ ubuntu-drivers devices`  

![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res01.PNG?raw=true)  

Install Gpu driver.  
~~~
// Update repository.  
$ sudo add-apt-repository ppa:graphics-drivers/ppa  
$ sudo apt update  

// Check recommeded driver can be used.  
$ apt-cache search nvidia | grep nvidia-driver-460  

//Install driver.  
$ sudo apt-get install nvidia-driver-460  

// Reboot.
$ sudo reboot  
~~~  
※ If some error occured, remove Nvidia related package and configure file.  
`$ sudo apt --purge autoremove nvidia*`  

Next, Check gpu driver is working.  

`$ nvidia-smi`  
  
![res](https://github.com/hololee/How-to-set-up-deeplearning-server/blob/main/resources/res02.PNG?raw=true)  



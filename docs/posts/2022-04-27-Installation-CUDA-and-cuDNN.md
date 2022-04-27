---
title: Installation of CUDA and cuDNN
parent: posts
nav_exclude: true
---
1.TOC
{:toc}

# Introduction
This post describes how to install CUDA and cuDNN in the base enviroment. But you are highly recommended to install cuda-toolkit and cuDNN with pytorch/tensorflow in the anaconda virtual enviroment.

# Check the compatibility (Optional)
- [Driver-CUDA](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#default-to-minor-version)
- [Pytorch-CUDA](https://pytorch.org/get-started/previous-versions/)
- [Tensorflow-CUDA](https://www.tensorflow.org/install/source#tested_build_configurations)

# Install CUDA via network (checked on 2080ti and 1080 ti, 27 April 2022)
Below commands are copied from the website [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=deb_network). It will install a **cuda-toolkit** and a **nvidia-driver** of the appropriate versions.
```shell
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/7fa2af80.pub
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/ /"
sudo apt-get update
# sudo apt list cuda*  # list all effective cuda versions (optional)
sudo apt-get -y install cuda  # you may specify the version of cuda to install, e.g., cuda-11-1
```
Reboot after the CUDA installation, and use the below command to check if CUDA was installed successfully:
```
nvidia-smi
```

# Install cuDNN
cuDNN can speed up the GPU computation based on CUDA.
## Download
[Download](https://developer.nvidia.com/rdp/cudnn-download) the cuDNN (you may need to sign up first) according to the version of you installed CUDA.
![Screenshot from 2022-04-27 14-12-18](https://user-images.githubusercontent.com/42603768/165452917-ae7d357a-8573-4b1a-bf3e-4b5fd4f06d24.png)

## Install
Install the downloaded `.deb` file:
```shell
sudo dpkg -i ~/Downloads/cudnn-local-repo-ubuntu2004-8.4.0.27_1.0-1_amd64.deb
```

# Supplementary content
## Removing all nvidia relative files.
In case encountering any error, below commands can be used to remove all files related to nvidia-cuda and nvidia-driver. Please use them after careful consideration:
```shell
# sudo rm /etc/apt/sources.list.d/cuda*
# sudo apt-get --purge remove "*cublas*" "cuda*" "nsight*" 
# sudo apt-get --purge remove "*nvidia*"
# sudo apt-get autoremove
# sudo apt-get autoclean
# sudo rm -rf /usr/local/cuda*
```
## GCC version problem
If you installed the **CUDA 10.1** of Ubuntu 18.04. You might will meet the problem of "GCC version is too high", the solution is:
```shell
sudo apt install build-essential
sudo apt -y install gcc-8 g++-8
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 8
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-8 8
```

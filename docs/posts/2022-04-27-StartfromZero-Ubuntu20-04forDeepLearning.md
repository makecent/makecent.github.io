---
title: "Start From Zero: Deep Learning on Ubuntu 20.04"
parent: posts
---

1. TOC
{:toc}
# 0. Introduction
..
# 1. Install Ubuntu 20.04
## 1.1 Create a bootable USB stick.
> Requirement: A 4GB or larger USB stick/flash drive

I refer the readers to the the offical tutorials for creating a bootable USB stick on [Windows](https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview), [masOS](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview), or [Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu#1-overview).

## 1.2 Install Ubuntu 20.04 desktop
I refer the readers to the [offical tutorial](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview). We can start from its [step-4](https://ubuntu.com/tutorials/install-ubuntu-desktop#4-boot-from-usb-flash-drive) since we have already created a bootable USB stack.

**Configuration adivces:**
- Don't use dual boot, i.e., install two operating systems (e.g., Ubuntu and Windows) in your computer.
- Install system on SSD drive if possible.
- *"Erase disk and install Ubuntu"* is good unless you are famialr with disk partition. Make sure that important data in the disk have been backed up.
- Make `your name` and `computer's name` short to have a nice prefix in the terminal:
![image](https://user-images.githubusercontent.com/42603768/165436583-226ce206-26ac-43e8-92e6-3f76831e1650.png)
- If you accidently configured some wrong/unwanted settings, reinstallation could be one of the simplest solution.
- Do not waste time on trying to install Windows apps on Ubuntu.
- Install the version of English.
<details>
  <summary>Got BIOS problems?</summary>
  
  **F12 not working?**
  1. *"F12 is the most common key for bringing up your system’s boot menu, but Escape, F2 and F10 are common alternatives."*
  2. *"If you’re unsure, look for a brief message when your system starts – this will often inform you of which key to press to bring up the boot menu."*
  3. If there is not brief message guiding you to press which key to get into the system’s boot menu (BIOS), Google the BIOS key of your motherboard.
  4. If pressing the right BIOS key (repeatedly/and holding) is still not working, it's probably because the Fast Boot is enable on your computer, which will skip the BIOS access. Google how to disable the Fast Boot for your motherboard.

  **Cannot boot from USB in BIOS?**
  Different motherboards have BIOS of different styles. Find and get into the setting of `Boot Menu`, and there should be the name of your USB flash in the list. Select your USB and continue the booting.
</details>

# 2. Configure Input Method 
Take the configuration of input source of Chinese as an example.
1. `Settings --> Region&Language --> Manage Installed Language --> Install/Remove Language --> check "Chinese" --> Apply` 
![Screenshot from 2022-04-27 14-39-44](https://user-images.githubusercontent.com/42603768/165456822-b5f59cd0-4294-489a-b03e-fc61b74240f0.png)
2. Open a terminal and run `ibus restart`:
![Screenshot from 2022-04-27 14-40-57](https://user-images.githubusercontent.com/42603768/165457048-e912f80d-4070-4f27-809e-214ab9804c90.png)
3. `Settings --> Region&Language --> "+" Input Sources --> Chinese (Intelligent Pinyin) --> Add`:
![Screenshot from 2022-04-27 14-42-04](https://user-images.githubusercontent.com/42603768/165457209-3fe0ea69-96ee-4f04-8995-5945b8d0c89f.png)
4. Configuration completed. Now you can use the shortcut `Super + Space` to switch between the input sources. The status is shown in the right-up corner：
![Screenshot from 2022-04-27 14-42-41](https://user-images.githubusercontent.com/42603768/165457310-e3f623f2-e0e1-4b55-8a46-db229e8195bb.png)

# 3. Install Chrome
```shell
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

# 4. Install PyCharm
Search Pycharm in `Software` and install the one you like:
![Screenshot from 2022-04-27 12-17-48](https://user-images.githubusercontent.com/42603768/165439907-01ce19cb-1975-4e92-8850-2de5792a764d.png)

> I installed pro because I have free license of student.

> External reading: [Ubuntu Software vs Software](https://askubuntu.com/questions/866755/differences-between-ubuntu-software-center-and-ubuntu-software-their-pros-and)

# 5. Install Git
```shell
sudo apt install git
```

# 6. Install Anaconda
Here we install the [miniconda](https://docs.conda.io/en/latest/miniconda.html#)
```shell
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh 
sudo rm Miniconda3-latest-Linux-x86_64.sh
conda update -n base -c defaults conda  # restart the terminal before run this line
```

# 7. Install GPU-driver
Nowadays, you don NOT have to install cuda-toolkit or cuDNN because they will come with the installation of pytorch/tensorflow. Therefore, you only need to install a NVIDIA driver. Check the compatibility of [GPU-Driver](https://www.nvidia.com/Download/index.aspx) (optional).
## 7.1 Add PPA repository
```shell
sudo add-apt-repository ppa:graphics-drivers
sudo apt update
ubuntu-drivers devices  # list all effetive drivers
# sudo apt list nvidia-driver*  # (alternative) list all effective drivers 
```
Output (1080ti, 27 April 2022):
![Screenshot from 2022-04-27 13-17-11](https://user-images.githubusercontent.com/42603768/165445679-dd30d3e0-effe-4905-8fb1-98cc7d87aedf.png)

## 7.2 Install driver
Choose one appropriate version, install it:
```shell
sudo apt install nvidia-driver-510
```
Reboot and check the installation:
```shell
reboot
nvidia-smi
```

# 8. Install CUDA and cuDNN (optional, not recommended)
As mentioned in Section 7, you are recommended to install cuda-toolkit and cuDNN with pytorch/tensorflow at the conda virtual enviroment. But if you'd like to install CUDA and cuDNN in the base enviroment. You can do the following:
## 8.0 Check the compatibility (Optional)
- [Driver-CUDA](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#default-to-minor-version)
- [Pytorch-CUDA](https://pytorch.org/get-started/previous-versions/)
- [Tensorflow-CUDA](https://www.tensorflow.org/install/source#tested_build_configurations)
## 8.1 Install CUDA via network (checked on 2080ti and 1080 ti, 27 April 2022)
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

## 8.2 Supplement
### Removing all nvidia relative files.
In case encountering any error, below commands can be used to remove all files related to nvidia-cuda and nvidia-driver. Please use them after careful consideration:
```shell
# sudo rm /etc/apt/sources.list.d/cuda*
# sudo apt-get --purge remove "*cublas*" "cuda*" "nsight*" 
# sudo apt-get --purge remove "*nvidia*"
# sudo apt-get autoremove
# sudo apt-get autoclean
# sudo rm -rf /usr/local/cuda*
```
### GCC version problem
If you installed the **CUDA 10.1** of Ubuntu 18.04. You might will meet the problem of "GCC version is too high", the solution
如果是在20.04上安装适配18.04的CUDA 10.1，会有一个GCC版本过高的问题，解决方法：
sudo apt install build-essential
sudo apt -y install gcc-8 g++-8
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 8
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-8 8

## 8.3 Install cuDNN
cuDNN can speed up the GPU computation based on CUDA.
### 8.3.1 Download cuDNN
[Download](https://developer.nvidia.com/rdp/cudnn-download) the cuDNN (you may need to sign up first) according to the version of you installed CUDA.
![Screenshot from 2022-04-27 14-12-18](https://user-images.githubusercontent.com/42603768/165452917-ae7d357a-8573-4b1a-bf3e-4b5fd4f06d24.png)

Install the downloaded `.deb` file:
```shell
sudo dpkg -i ~/Downloads/cudnn-local-repo-ubuntu2004-8.4.0.27_1.0-1_amd64.deb
```

# 9. Create virtual enviroment for Pytorch/TensorFlow
## [Pytorch](https://pytorch.org/get-started/locally/)
```
conda create -n pyt
conda activate pyt
conda install pytorch torchvision torchaudio -c pytorch  # you can specify the version, e.g., cudatoolkit=11.3
conda list
```

## TensorFlow
```shell
# conda create -n tf-gpu tensorflow-gpu  # configure with one command, including cuda-toolkit and cuDNN
conda create -n tf-gpu
conda activate tf-gpu
conda install tensorflow-gpu  # you can specify the versions, e.g., tensorflow-gpu=2.4.1 cudatoolkit=10.1
conda list
```

- Conda virtual enviroment is flexible and simple to use. It can install cuda-toolkit and cuDNN easily. We can configure multiple envs with different versions of cuda/pytorch/tensorflow and switch among them quickly. 
- Note that if you have installed cuda in the base enviroment, i.e., completed the Section 8, it's recommended to install the same version of the cuda-toolkit in the virtual env to avoid possible version conflict. Alternatively, you can install pytorch/tensorflow sololy without cuda by using `pip install` (instead of `conda install`). In this case, your virtual env doesn't contain cuda and it will use the cuda installed in the base enviroment.

# 9. Configure PyCharm
以tf-gpu为例。主要的工作是将刚刚用conda创建的名为tf-gpu环境，作为基于tensorflow的项目的python interpreter。Ctrl+Alt+S或通过File->Settings进入设置，并进入Project: xxx栏目中的Project Interpreter：
​

编辑

切换为居中
添加图片注释，不超过 140 字（可选）
点击右上角的齿轮图标，并在弹出的列表里点击Add：
​

编辑

切换为居中
添加图片注释，不超过 140 字（可选）
在左边栏目中选中Conda Environment，在右侧选择Exsiting environment，一般Pycharm能自动检测到conda软件和已有的conda环境，选择刚才创建的tf-gpu环境即可。最后当然是点击OK。
如此，这个Project就配置上tf-gpu这个环境了：
​

编辑

切换为居中
添加图片注释，不超过 140 字（可选）
这个界面只会显示python packages，所以看不到cuda和cudnn。如需要添加，删除和升级package，可分别通过列表右侧的加（+），减（-）以及上三角符号完成。
当然这些操作都可以通过在命令行里面用conda install或pip install来执行，前提是记得用conda activate tf-gpu进入虚拟环境，否则就是安装在base的主环境了。
10. 硬盘管理
10.1 只修改挂载选项
由于安装系统只影响装系统的硬盘，其他硬盘的内容和设置不会受影响。所以如果其他硬盘之前已经设置得当，那只需要简单设置一下挂载选项即可。进入Disks软件（系统自带）：
​

编辑

切换为居中
添加图片注释，不超过 140 字（可选）
左边显示了电脑上的硬盘。装了系统的硬盘无须理会，对别的硬盘进行设置。选中要设置的硬盘，如果硬盘已经处在挂载状态，先点击Unmount（黑色正方形）取消挂载；点击齿轮图标，如果你没有给硬盘命名过label，则点击Edit Filesystem，在弹出的对话框内给硬盘取个lable名字。之后选择Edit Mount Options：
​

编辑

切换为居中
添加图片注释，不超过 140 字（可选）
关闭Use Session Default，进行自定义设置。勾选Mount at system startup可以让系统开机时自动挂载该硬盘；Show in user interface一般也勾选；重要的修改Identify As，我一般选择by-label。选择完后Mount Point会自动修改，这就是以后访问该硬盘的路径。这样设置的硬盘路经比较短，也有辨识度。之后点击OK即可。设置完之后，可以再将硬盘挂载上电脑，点击Mount按钮（与Unmount按钮是同一个）即可。挂载后就可以去文件系统 (Files) 的Other Locations里访问该硬盘了。
你可能会发现你对硬盘没有什么权限，甚至无法创建新的文件夹。那是因为该硬盘默认归root所有，可以考虑将该硬盘改为归你所有：
sudo chown -R $USER:$USER /mnt/disk-label  #注意改成你自己的硬盘路径(Mount Point)
10.2 给硬盘格式化，分区等
如果想直接对硬盘进行改动，比如格式化，分区等（这些操作都非常危险，对硬盘里数据进行备份），可以用GParted软件：
sudo apt install gparted
GParted软件使用其实非常简单，功能也很多。这里不再详细介绍，网上有很多攻略。需要注意的是，如果你要用GParted对系统盘进行操作，如划分出一部分新的区域安装Windows，在系统盘运行的状态下是不可行的。这个时候你可以插入之前的U盘，重启电脑，按第一节讲的进入BIOS并选择以U盘里的系统开机，之后选择试用Ubuntu (Try Ubuntu) 进入U盘内的试用版Ubuntu，里面已经自动安装好了GParted，用它便可以系统盘进行分区和格式化了。
11. 安装TeamViewer
免费的远程操作电脑软件。
wget https://download.teamviewer.com/download/linux/teamviewer_amd64.deb
sudo apt install -y ./teamviewer_amd64.deb
sudo rm teamviewer_amd64.deb
打开TeamViewer，通过Extras->Options->勾选Start TeamViewer with system。但是重启后发现TeamViewer并没有随开机启动。
sudo systemctl start teamviewerd.service
sudo systemctl enable teamviewerd.service
12. 结语
部分材料借鉴于：
TensorFlow - Anaconda documentation
​
docs.anaconda.com
Li-Wen's Blog
​
liwen.site

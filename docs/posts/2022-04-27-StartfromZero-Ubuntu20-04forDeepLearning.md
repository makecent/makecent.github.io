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

We follow the offical tutorials for creating a bootable USB stick on [Windows](https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview), [masOS](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview), or [Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu#1-overview).
## 1.2 Install Ubuntu 20.04 desktop
We follow the [offical tutorial](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview). We can start from its [step-4](https://ubuntu.com/tutorials/install-ubuntu-desktop#4-boot-from-usb-flash-drive) since we have already created a bootable USB stack.

### `F12` not working?

1. *"F12 is the most common key for bringing up your system’s boot menu, but Escape, F2 and F10 are common alternatives."*
2. *"If you’re unsure, look for a brief message when your system starts – this will often inform you of which key to press to bring up the boot menu."*
3. If there is not brief message guiding you to press which key to get into the system’s boot menu (BIOS), Google the BIOS key of your motherboard.
4. If pressing the right BIOS key (repeatedly/and holding) is still not working, it's probably because the Fast Boot is enable on your computer, which will skip the BIOS access. Google how to disable the Fast Boot for your motherboard.

### Cannot boot from USB in BIOS?

Different motherboards have BIOS of different styles. Find and get into the setting of `Boot Menu`, and there should be the name of your USB flash in the list. Select your USB and continue the booting.

### Other adivces

1. Don't use dual boot, i.e., install two operating systems (e.g., Ubuntu and Windows) in your computer.
2. Install system on SSD drive if possible.
3. *"Erase disk and install Ubuntu"* is good unless you are famialr with disk partition. Make sure that important data in the disk have been backed up.
4. Make `your name` and `computer's name` short to have a nice prefix in the terminal:
![image](https://user-images.githubusercontent.com/42603768/165436583-226ce206-26ac-43e8-92e6-3f76831e1650.png)
5. If you accidently configured some wrong/unwanted settings, reinstallation could be one of the simplest solution.
6. Do not waste time on trying to install Windows apps on Ubuntu.
7. Install the version of English.

# 2. Configure Input Method 
0. Chinese as an example.
1. `Settings --> Region&Language --> Manage Installed Language --> Install/Remove Language --> check "Chinese" --> Apply` 
2. Open a terminal and run `ibus restart`
3. `Settings --> Region&Language --> "+" Input Sources --> Chinese (Intelligent Pinyin) --> Add`
4. Configuration completed. Now you can use the shortcut `Super + Space` to switch between the input sources. The status is shown in the right-up corner.

# 3. 安装Chrome
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
4. 安装PyCharm
PyCharm是著名的Python开发环境软件。
直接在Ubuntu自带的Ubuntu Store里搜索Pycharm，便可以直接找到三个版本的Pycharm，安装即可。我个人装的是Pycham Pro，学生可以免费使用，否则需要购买激活。
5. 安装Git
Git是代码版本管理软件。
sudo apt install git
6. 安装Anaconda
Anaconda是用来管理环境和python库的软件。
这里选择安装轻量版的Miniconda3，在Terminal里面执行下列命令即可，建议全程回车（yes）使用默认设置：
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh #下载
sh Miniconda3-latest-Linux-x86_64.sh  #执行安装文件
sudo rm Miniconda3-latest-Linux-x86_64.sh  #删除安装文件
conda update -n base -c defaults conda
使用默认设置安装之后会自动添加conda到环境变量，但需要重新启动命令行生效。
7. 安装CUDA，显卡驱动和cuDNN
7.0 确定版本
找到与自己的GPU适配的CUDA版本：
Tensorflow-CUDA: https://www.tensorflow.org/install/source;
2. Pytorch-CUDA: PyTorch;
3. GPU-Driver: Download Drivers.
4. Driver-CUDA: CUDA Compatibility;
如果对tensorflow和pytorch版本没有要求，可跳过1，2步。
7.1 安装cuda，自动安装驱动
# 彻底清理nvidia驱动和cuda相关，出现问题才执行。
# sudo rm /etc/apt/sources.list.d/cuda*
# sudo apt-get --purge remove "*cublas*" "cuda*" "nsight*" 
# sudo apt-get --purge remove "*nvidia*"
# sudo apt-get autoremove
# sudo apt-get autoclean
# sudo rm -rf /usr/local/cuda*
在cuda archive网页选择自己想要的cuda版本，按指示下载安装（本地）。或者直接联网安装(deb-network， 建议)，下面是最新的(14.04.2022)Ubuntu 20.04联网安装cuda的命令：
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/7fa2af80.pub
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda #自动安装合适的cuda版本（可以通过加后缀来指定cuda版本）；附带合适的GPU驱动！！！
如果是在20.04上安装适配18.04的CUDA 10.1，会有一个GCC版本过高的问题，解决方法：
sudo apt install build-essential
sudo apt -y install gcc-8 g++-8
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 8
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-8 8
安装完成后要重启生效，重启后用'nvidia-smi'命令看看是否成功安装吧。
7.2 安装cuDNN
cuDNN是加速CUDA GPU运算的扩展包，非必须。
进入cuDNN官网下载界面（下载cuDNN需要登陆，注册一个账号登陆即可），根据CUDA版本选择cuDNN版本，展开：
​

编辑

切换为居中
添加图片注释，不超过 140 字（可选）
这里也还是暂时不支持Ubuntu20.04，选择18.04的文件下载即可（三个都下载）。在下载的路径上执行安装程序：
cd ~/Downloads  #进入下载好的三个文件的路径
sudo dpkg -i libcudnn*  #同时安装
#sudo dpkg -i libcudnn7_7.6.5.32-1+cuda10.2_amd64.deb  #逐个安装
#sudo dpkg -i libcudnn7-dev_7.6.5.32-1+cuda10.2_amd64.deb
#sudo dpkg -i libcudnn7-doc_7.6.5.32-1+cuda10.2_amd64.deb
8. 用conda创建自带CUDA和cuDNN的虚拟环境（可选）
配置TensorFlow环境：
#conda create -n tf-gpu tensorflow-gpu  #一行完成。安装最新TF，自动配置合适的cuda和cudnn
conda create -n tf-gpu  #创建环境，命名可自定义
conda activate tf-gpu  #进入tf-gpu虚拟环境
conda install tensorflow-gpu=2.1.0 cudatoolkit=10.1  #根据需求自行更改
conda list -n tf-gpu  #查看tf-gpu环境下安装了哪些package
配置Pytorch环境：按照Pytorch官网指示
conda create -n pyt #创建环境，命名可自定义
conda activate pyt #进入pyt虚拟环境
conda install pytorch torchvision cudatoolkit=10.1 -c pytorch  #选择合适的参数
用conda install安装tensorflow和pytorch的优势非常明显，conda会自动帮我们安装好合适的cuda和cuDNN（也可以指定cuda版本），可以创建多个虚拟环境使用不同的CUDA版本，非常灵活。
建议在虚拟环境中安装与主环境相同版本的CUDA。如果你既在主环境上装了cuda（上节），又使用该节的方式在虚拟环境中安装了不同版本的CUDA，则在虚拟环境中的CUDA可能会和主环境的CUDA有所冲突。解决方法是：
要么直接跳过第6节，不在主环境下安装CUDA，只使用虚拟环境。
要么不在虚拟环境中安装cuda。由于conda install安装tensorflow或pytorch是默认安装CUDA的，所以需要用pip install来安装tensorflow和pytorch。这样就不会在虚拟环境里安装cuda和cuDNN。而使用此虚拟环境运行代码时，系统会自动使用电脑（主环境）上的cuda和cuDNN。
9. 配置PyCharm
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

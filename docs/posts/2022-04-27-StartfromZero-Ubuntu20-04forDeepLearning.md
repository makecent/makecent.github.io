---
title: "Start From Zero: Deep Learning on Ubuntu 20.04"
parent: Posts
has_toc: true
---

# 0. Introduction
{: .no_toc }
This post documents how I configure a brand new computer into a work station that could be used for deep learning. It was first posted on the ZhiHu [in Chinese](https://zhuanlan.zhihu.com/p/142923008), which is no longer updated. This page is maintained by me to be up-to-date. Last update: 27 April 2022.

# 1. Install Ubuntu 20.04
## 1.1 Create a bootable USB stick.
> Requirement: A 4GB or larger USB stick/flash drive

Follow the the offical tutorials for creating a bootable USB stick on [Windows](https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview), [masOS](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview), or [Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu#1-overview).

## 1.2 Install Ubuntu 20.04 desktop
Follow the [offical tutorial](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview). We can start from its [step-4](https://ubuntu.com/tutorials/install-ubuntu-desktop#4-boot-from-usb-flash-drive) since we have already created a bootable USB stack.

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
Nowadays, you don NOT have to install cuda-toolkit or cuDNN because they will come with the installation of pytorch/tensorflow using conda. Therefore, you only need to install a NVIDIA driver. Check the compatibility of [GPU-Driver](https://www.nvidia.com/Download/index.aspx) (optional).
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
Outputs:

![Screenshot from 2022-04-27 15-42-16](https://user-images.githubusercontent.com/42603768/165467361-d213cf14-8aaa-469d-aaa7-16e3041e70e6.png)

Note that the `CUDA Version: 11.6` in the above picture does NOT mean that a CUDA is installed.

# 8. Install Pytorch/TensorFlow
Follow the official installation tutorials of [Pytorch](https://pytorch.org/get-started/locally/) and [Tensorflow](https://www.tensorflow.org/install). Alternatively, you may run the following given commands to install pytorch/tensorflow in a new conda env (cuda and cuDNN are all installed automatically).
## Pytorch
```
conda create -n pyt  # replace 'pyt' with name you like
conda activate pyt
conda install pytorch torchvision torchaudio -c pytorch
conda list
```

## TensorFlow
```shell
conda create -n tf-gpu
conda activate tf-gpu
conda install tensorflow-gpu 
conda list
```
Noted that you can specify version that you want, e.g., `tensorflow-gpu=2.4.1 cudatoolkit=10.1` and `pytorch=1.11.0 cudatoolkit=11.3`.
- Conda virtual enviroment is flexible and simple to use. It can install cuda-toolkit and cuDNN easily. We can configure multiple envs with different versions of cuda/pytorch/tensorflow and switch among them quickly. 
- If you have installed a global cuda in the system, and a different version of the cuda-toolkit in the virtual env, you then might encounter the error of version conflict. You can install pytorch/tensorflow solely by using `pip install` (instead of `conda install`). In this case, your virtual env will use the cuda installed in the system.

# 9. Configure PyCharm
To use the created conda virtual in Pycharm, you need to configure it as the `Python Interpreter` of your project. Open the `File --> Settings --> Project --> Python Interpreter`：
![Screenshot from 2022-04-27 16-06-48](https://user-images.githubusercontent.com/42603768/165471644-a27e4838-5e37-46e9-926b-bf6a873b1087.png)

Click the gear icon at the right-up corner, chose 'Add':
![Screenshot from 2022-04-27 16-11-18](https://user-images.githubusercontent.com/42603768/165472491-5b0ffca1-61cf-4dc4-9ddb-af2c218393e1.png)

Choose `Conda Enviroment --> Exsiting enviroment`, then configure the executable python path of your conda env:
![Screenshot from 2022-04-27 16-23-22](https://user-images.githubusercontent.com/42603768/165474863-54fb60f5-d04b-4fc5-931f-87ab64808a49.png)

The executable python path will be automatically detected by the Pycharm. In case not, it normally locates at `/home/<user_name>/miniconda3/env/<env_name>/bin/python`.

# 10. Install TeamViewer (optional)
Control your computer remotely:
```shell
wget https://download.teamviewer.com/download/linux/teamviewer_amd64.deb
sudo apt install -y ./teamviewer_amd64.deb
sudo rm teamviewer_amd64.deb
```
TeamViewer didn't restart with the system even after I checked the box `Extras --> Options --> Start TeamViewer with system`. The solution I found: 
打开TeamViewer，通过Extras->Options->勾选Start TeamViewer with system。但是重启后发现TeamViewer并没有随开机启动。
```shell
sudo systemctl start teamviewerd.service
sudo systemctl enable teamviewerd.service
```

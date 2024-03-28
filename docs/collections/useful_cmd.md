---
layout: default
title: Console
parent: Collections
nav_order: 6
---
1. TOC
{:toc}
# Miscellaneous
### Get the **number of files**

```shell
ls . | wc -l
ls /foo/*.imgs | wc -l
```
### Get the **disk size** of folder

```shell
du -hs ./datasets
```
### Get disk space info
```she;;
df -h
```
### Create and delete files

```shell
touch foo.txt
mkdir a_folder
rm foo.txt
rm -r a_folder
```

### Check SSD status
check which disk current path `.` is placed.
```shell
df . -h 
```
output:
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/nvme0n1p2  3.5T  973G  2.4T  30% /
```
check name and `rotation` of all disk:
```shell
lsblk -o NAME,ROTA  
```
output:
```shell
NAME        ROTA
nvme1n1        0
└─nvme1n1p1    0
nvme0n1        0
├─nvme0n1p1    0
└─nvme0n1p2    0
```
`ROTA=0` means the SSD, `1` denotes the HDD.
### Compression

Compress and Decompress:
```shell
tar -cvzf a_folder.tar.gz a_folder
tar -xvzf a_folder.tar.gz  # decompression
unzip a_folder.zip  # decompression
```
Split and Concat:
```shell
split -b 1024m -d --verbose a_folder.tar.gz a_folder.tar.gz.part_
cat a_folder.tar.gz.part_* > a_folder.tar.gz
```
``-b``: split file by size. (use ``-n`` to split by number)
``-d``: use numeric suffixes starting at 0, not alphabetic;

### Copy huge amount of files

```shell
rsync -ahW --no-i-r --info=progress2 source destination
# push/pull to remote
rsync -ahW --no-i-r --info=progress2 source foo@158.132.21.81:/home/foo/destination
rsync -ahW --no-i-r --info=progress2 foo@158.132.21.81:/home/foo/source destination
```
<details>
  <summary>Details</summary>

- `-a`: keep file information, including owners, permissions, etc.
- ``-h``: make output human-readable.
- ``-W``: copy files whole (w/o delta-xfer algorithm), faster.
- ``--no-i-r``: scan files before copying, rather than at the same time. Faster when lots of files.
- ``--info=progress2``: display a progress bar.
- ``--dry-run``: perform a trial run that doesn’t make any changes (and produces mostly the same output as a real run).
- ``source`` and ``destination``: the source file/folder and destination folder.
- ``source/``: If a trailing slash added, the **content** in ``source`` will be copied into the ``destination``. So if ``destination`` doesn't exist or is empty, this works like a combination of copy and rename.
</details>

### Kill all python
```shell
killall python
```
### Release GPU memory
Sometimes the GPU memory was not released after the training or a crash, which can be checked via the GPU memory assumption shown by the `nvidia-smi`. If you are sure that's a python script, you may directly run `killall python`, which could be danguours as it will kill all python process. Or
1. You can kill the process with the PID if it was shown in `nvidia-smi`:
```shell
kill -9 $PID
```
2. If the PID is not shown via `nvidia-smi`, you may check the GPU memory via:
```shell
sudo fuser -v /dev/nvidia*
```
### Run Multiple commands
You can execute multiple commands in line, e.g.:
```shell
python train1.py; python train2.py; python test.py
```
, which will execute the commands sequentially.
Available delimeters:
- `|`:   pipes (pipelines) the standard output (stdout) of one command into the standard input of another one. Note that stderr still goes into its default destination, whatever that happen to be.
- `|&`:  pipes both stdout and stderr of one command into the standard input of another one. Very useful, available in bash version 4 and above.
- `&&`: executes the right-hand command of `&&` only if the previous one **succeeded**.
- `||`:  executes the right-hand command of `||` only it the previous one **failed**.
- `;`:   executes the right-hand command of `;` always **regardless** whether the previous command succeeded or failed. Unless set -e was previously invoked, which causes bash to fail on an error.

To run program after a exsiting command finished, one can use the short-cut `Ctrl + Z` to first suspend the running command and then run:
```shell
fg ; following_command
```

### Save print output
```shell
your_command |& tee output.txt
```
Other syntax, copied from the [stackoverflow](https://askubuntu.com/a/731237/940404):
```shell
          || visible in terminal ||   visible in file   || existing
  Syntax  ||  StdOut  |  StdErr  ||  StdOut  |  StdErr  ||   file   
==========++==========+==========++==========+==========++===========
    >     ||    no    |   yes    ||   yes    |    no    || overwrite
    >>    ||    no    |   yes    ||   yes    |    no    ||  append
          ||          |          ||          |          ||
   2>     ||   yes    |    no    ||    no    |   yes    || overwrite
   2>>    ||   yes    |    no    ||    no    |   yes    ||  append
          ||          |          ||          |          ||
   &>     ||    no    |    no    ||   yes    |   yes    || overwrite
   &>>    ||    no    |    no    ||   yes    |   yes    ||  append
          ||          |          ||          |          ||
 | tee    ||   yes    |   yes    ||   yes    |    no    || overwrite
 | tee -a ||   yes    |   yes    ||   yes    |    no    ||  append
          ||          |          ||          |          ||
 n.e. (*) ||   yes    |   yes    ||    no    |   yes    || overwrite
 n.e. (*) ||   yes    |   yes    ||    no    |   yes    ||  append
          ||          |          ||          |          ||
|& tee    ||   yes    |   yes    ||   yes    |   yes    || overwrite
|& tee -a ||   yes    |   yes    ||   yes    |   yes    ||  append
```
### Add the current directory to PythonPath
```terminal
# Windows
$env:PYTHONPATH += ";$pwd"
# Linux
PYTHONPATH=$PWD:$PYTHONPATH
```
### Kill port 
```shell
sudo lsof -i:7777
kill $PID
```

### Print the public IP
```shell
curl ifconfig.me
```

# tmux
start new:

    tmux

start new with session name:

    tmux new -s myname

attach:

    tmux a (or at, or attach)

attach to named:

    tmux a -t myname

list sessions:

    tmux ls

kill session:

    tmux kill-session -t myname
    
kill all sessions:
   
    tmux kill-server
    
tmux command mode (inside tmux)

    Ctrl + b
    
split current window horizontally (top-down)

    ctrl + b + "
  
split current window vetically (left-right)

    ctrl + b + %

nevigate among windows

    ctrl + b + left/up/right/down

nevigate among sessions

    ctrl + s + up/down
  
## Shortcuts in tmux command mode
    ?:   list shortcuts
    s:   list sessions and chose one to switch to (recommended)
    d:   detach
    x:   kill current window
    c:   create a window
    w:   list windows
    new: create a new session
  
# Tensorboard on remote server
on server:
```shell
tensorboard --logdir <path> --port 7777
```
on local host:
```shell
ssh -N -L 7777:localhost:7777 luchongkai@158.132.21.81
```

# Mount on remote directory
Using the `sshfs` (require `root`). 
Here is an example that I want the training results produced from remote server to be saved on local machine (so I don't have to manually copy it), referring to an [external solution](https://superuser.com/questions/616182/how-to-mount-local-directory-to-remote-like-sshfs)
```
localusername@localmachine: ssh username@server -R 10000:localmachine:22
username@server: cd path2remote_project/
username@server: sshfs -p 10000 -o idmap=user,nonempty localusername@127.0.0.1:path2local_project/results results
```

# TeamViewer to a headless server
```shell
sudo apt-get install xserver-xorg-video-dummy
sudo nano /usr/share/X11/xorg.conf.d/xorg.conf
```
Paste the below content into the `xorg.conf`
```
Section "Device"
    Identifier "DummyDevice"
    Driver "dummy"
    VideoRam 256000
EndSection

Section "Screen"
    Identifier "DummyScreen"
    Device "DummyDevice"
    Monitor "DummyMonitor"
    DefaultDepth 24
    SubSection "Display"
        Depth 24
        Modes "1920x1080_60.0"
    EndSubSection
EndSection

Section "Monitor"
    Identifier "DummyMonitor"
    HorizSync 30-70
    VertRefresh 50-75
    ModeLine "1920x1080" 148.50 1920 2448 2492 2640 1080 1084 1089 1125 +Hsync +Vsync
EndSection
```
Reboot to bingo.

Someone mentioned that instead of `/usr/share/X11/xorg.conf.d/xorg.conf`, create `.conf` at `/etc/X11/xorg.conf` worked for them. You may have a try.

# Using SSH tunneling for SOCKS5 proxy
Refer to this answer: [Can I use the SSH tunnel under a VPN as a VPN server?]([https://serverfault.com/questions/1126894/can-i-use-the-ssh-tunnel-under-a-vpn-as-a-vpn-server?noredirect=1#comment1469814_1126894](https://serverfault.com/a/1126935/1011988)) 

# Use conda-wise CUDA to complie package
We often have a cudatoolkit installed in each conda environment and a CUDA installed in the system. Their version could be different. When we complie a packages that requires CUDA, it uses the system-wise CUDA by default. Which could have version compatability problem.
As change the version of system-wise CUDA is tricky and danguous. We often want to **compile with the conda-wise CUDA**.

You can use the below commands to add the libraries inside a conda environment to the PATH (note this works temporally and you need re-run them if you re-open another terminal):
```shell
export CUDA_HOME=/home/louis/miniconda3/envs/env_name   # replace this path to your env
export PATH=$CUDA_HOME/bin:$PATH
export LD_LIBRARY_PATH=$CUDA_HOME/lib:$LD_LIBRARY_PATH
```
Then, run `ncvv -V` to check if the version is equal to the one in your environment or the system-wise one. 

You may sometimes do not have a (complete) cudatoolkit inside your environment. In this case, you could manually install one by `conda install -c conda-forge cudatoolkit-dev`.

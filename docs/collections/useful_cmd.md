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
### Multiple commands
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

    Ctrl + B
    
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

# Kill port 
```shell
sudo lsof -i:7777
kill $PID
```

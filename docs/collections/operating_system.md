---
layout: default
title: Operating System
parent: Collections
nav_order: 5
---

# Ubuntu
## Basic ##
`alt + space` -> 'Always on Top': to pin a window/app

`alt + F2`： run a command quickly, without opening a terminal.

## Files ##
`ctrl + L`: browse with path string

`ctrl + D`: bookmark current directory

## Terminal ##
`Preferences -> Shortcuts`: view all terminal shortcuts

`ctrl + alt + T`: create a new terminal

`shift` + `CtrlC`/`CtrlV`: copy and paste in terminal

`ctrl + U`: delete whole line

`xkill`: use cursor to select a window to kill. 

## Screenshot ##
  1. `PrtScrn` to take a screenshot of the **desktop**.
  2. `alt + PrtScrn` to take a screenshot of a **window**.
  3. `shift + PrtScrn` to take a screenshot of an area you **select**.

## Alias
You may set alias for frequently used commands. You can send alias to `.bashrc`, e.g.:
```
echo "alias server='ssh user@<SERVER_IP>'" >> ~/.bashrc
echo "alias mmlab='conda activate open-mmlab'" >> ~/.bashrc
echo "alias project='cd /home/louis/my_project'" >> ~/.bashrc
source ~/.bashrc
```
or directly edit the `.bashrc` file, writting the alias at its end:
```
nano ~/.bashrc
source ~/.bashrc
```

## Miscellaneous

### Cannot open Applications
My problem is that I cannot open Pycharm and Ubuntu Software (snap) by clicking the icon. Below helps:
```teminal
#sudo apt install snapd  if `snap command not found`
sudo snap remove snap-store
sudo snap install snap-store
```

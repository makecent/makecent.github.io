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
### Create and delete files

```shell
touch foo.txt
mkdir a_folder
rm foo.txt
rm -r a_folder
```

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
   meanings of arguments:
   
   - ``-a``: keep file information, including owners, permissions, etc.
   
   - ``-h``: make output human-readable.
   
   - ``-W``: copy files whole (w/o delta-xfer algorithm), faster.
   
   - ``--no-i-r``: scan files before copying, rather than at the same time. Faster when lots of files.
   
   - ``--info=progress2``: display a progress bar.
   
   - ``--dry-run``: perform a trial run that doesn’t make any changes (and produces mostly the same output as a real run).
   
   - ``source`` and ``destination``: the source file/folder and destination folder.
   
   - ``source/``: If a trailing slash added, the **content** in ``source`` will be copied into the ``destination``. So if ``destination`` doesn't exist or is empty, this works like a combination of copy and rename.
   
</details>

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
    s:   list sessions and chose one to switch to
    d:   detach
    x:   kill current window
    c:   create a window
    w:   list windows

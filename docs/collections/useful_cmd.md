---
title: Terminal
parent: Collections
nav_order: 6
---
1. TOC
{:toc}
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
```
<details>

``-a``: keep file information, including owners, permissions, etc. \
``-h``: make output human-readable. \
``-W``: copy files whole (w/o delta-xfer algorithm), faster. \
``--no-i-r``: scan files before copying, rather than at the same time. Faster when lots of files. \
``--info=progress2``: display a progress bar. \
``--dry-run``: perform a trial run that doesn’t make any changes (and produces mostly the same output as a real run). \
``source`` and ``destination``: the source file/folder and destination folder. \
``source/``: If a trailing slash added, the **content** in ``source`` will be copied into the ``destination``. So if ``destination`` doesn't exist or is empty, this works like a combination of copy and rename.
   
</details>

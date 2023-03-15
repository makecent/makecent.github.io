---
layout: default
title: Pycharm
parent: Collections
nav_order: 4
---

### Speed up Pytorch Debugging
Tick on the ``Settings -> Build, Extension,Depolyment -> Python Debugger -> Gevent compatible`` to make debug on pytorch code (GPU) fast.

### Speed up Visiting Externel Packages
`Settings -> Python Interpreter -> Show All -> Show path for the selected interpreter -> Add (path to the package)`. Then this path will show in you project structure (in `Externel Libararies`).

### Useful short cuts
`Help -> Keyboard shortcuts PDF` to get the shortcuts cheat sheet.

`Ctrl + Shift + F`: Search in project.

`Ctrl + P`: Show parameters hints. (cursor in function parentheses)

`Ctrl + D`: Copy and paste a new line.

`Ctrl + Alt + L`: Reformat code. Auto-fix space, indent, ...

`Ctrl + Alt + Shift + L`: Reformat code dialog. Reformatting with options, e.g., enable imports optimization.

`Ctrl + Alt + O`: optimize imports

`Ctrl + LeftClick`: Go to function file.

`Shift + Enter`: Go to next line.

### Resolve updating index forever
Commonly it's because you put datasets under the content root of your project. Pycharm takes forever to index these millions of images...
Right-clik the datasets folder and `Mark Directory as Excluded` will help. 
If still not help, try adding the regex-file-name (e.g., *.jpg) or folder-name (e.g. data) into the `Settings -- Editor -- File Types-- Ignored Files and Folders -- + -- <subdirectory/filetype-that-you-want-to-ignore>`

# Remote development

## The older solution:

### File synchronization
Create a `development` configuration (`Tools --> Development --> Configuration`).

![image](https://user-images.githubusercontent.com/42603768/225193508-60870ea0-7440-4ea9-9c5c-bdd97d23162b.png)

You may need set a `SSH` configuration first:

![image](https://user-images.githubusercontent.com/42603768/225187544-3b9c3f8c-a9a6-448c-b23a-04acffc175b8.png)

Configure `Mapping` to map the project path:

![image](https://user-images.githubusercontent.com/42603768/225193987-4bc39838-c90d-4262-8d9f-f7ddc3fead5e.png)

Configure `Excluded Path` to specify paths on local/server that will NOT be synchronized, e.g., directories of datasets and saved results on the server.

![image](https://user-images.githubusercontent.com/42603768/225194054-e6768f54-9b87-49af-bdda-3c90780e5f9d.png)

After the configuration, you can now synchronize the codes by uploading/downloading files between the local machine and the server. (You may configure an auto-sync as needed).

![image](https://user-images.githubusercontent.com/42603768/225197863-7e1e4705-e885-4baf-b48e-2e387aa6e294.png)

### Remote interpreter:

![image](https://user-images.githubusercontent.com/42603768/225198586-e442e546-b25d-4d5f-a1c2-d698e8c27968.png)

![image](https://user-images.githubusercontent.com/42603768/225199053-3a86584e-4874-4cba-b60e-7a9aff006525.png)

![image](https://user-images.githubusercontent.com/42603768/225199760-0f9b681e-3461-4cab-9340-cb2cda50d84e.png)


You may use the command `which python` (after `conda activate target-env`) to get the python interpreter path of an exsiting `conda` environment.

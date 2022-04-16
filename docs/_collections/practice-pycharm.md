---
title: "Pycharm practice"
---

#### Speed up Pytorch Debugging
Tick on the ``Settings -> Build, Extension,Depolyment -> Python Debugger -> Gevent compatible`` to make debug on pytorch code (GPU) fast.

#### Speed up Visiting Externel Packages
`Settings -> Python Interpreter -> Show All -> Show path for the selected interpreter -> Add (path to the package)`. Then this path will show in you project structure (in `Externel Libararies`).

#### Useful short cuts
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

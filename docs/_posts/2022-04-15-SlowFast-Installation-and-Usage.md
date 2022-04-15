---
title:  "Site under construction"
categories: 
  - Coding
---

# Installation (15 April 2022)
```bash
foo@bar:~$ whoami
git clone https://github.com/facebookresearch/SlowFast.git
cd SlowFast
git clone https://github.com/facebookresearch/detectron2.git

conda create -n slowfast python=3.8
conda activate slowfast
conda install pytorch torchvision -c pytorch
python -m pip install -e detectron2
nano setup.py     #手动将PIL改称pillow
pip install -v -e .
rm -rf detectron2
```

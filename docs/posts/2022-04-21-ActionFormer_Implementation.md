---
title: ActionFormer Implementation
---

# Installation
```shell
git clone https://github.com/happyharrycn/actionformer_release.git
cd actionformer_release
conda create -n actionformer python=3.8
conda activate actionformer
conda install pytorch torchvision -c pytorch
conda install pyyaml h5py joblib tensorboard pandas
cd ./libs/utils
python setup.py install --user
cd ../..
mkdir data
```

# [Download thumos14](https://public.boxcloud.com/d/1/b1!b1OH9XcalSLZcmDN714EtP3I78TzCEBPwwuZUOyzEsHeoGhAZtK3OMROJhnrxV3nNAAiOh3Zi6JA_E5nmuAOavKca2ygoD07dtDq0sjVfcbkWXXLSTuPCgM0DQV9VRKfp9Mx7S3f83l-upm4y4nXwZxwmcFs7MoDlfrFSQaZsMCp4-KVX1XZHUUH_rNgZc4yDwqEdXoCdzWY9gsqOuA-XNzMqtoEmD8MtfKWbIl_slPv6t_2cbpH7j-6HV4Hpmzk4T9168E_-zbUn_tTRaA9ScFmsV_bg8xAa89lwPShvAp8GBHOY6c980KpTjjosGQfe0N8TM7KHaj116aTqLm7n0mO3CUt1L-CIKKDut32s1qXuLhg3RiJlUEz6KFPcis4fKgHiy3Pdt0R6Vrr1V67L4695SelQAArQy1C-p1wsiY_JycArTRy9bIb2WBRy1Vu4kXynu7ccZOMQax_FshNz3-oLEY1WAsy20HJnkmbhLO1NaMfYl9FqXNE9BZGuapeqO7z8e7-_NxCde_SsyGukX1PRMIPUQcbdKG-oDiYxaI0yblSSQeuKmeAcWO5qN0LlOuOiYiJDi5Ks72dqlrGPX-KnwWnzir77NGXvuHveQkOEy_VJ6ov3wGfQsoNQs_145yGZ5OwnI4E6lN5QoJZ9pTTzeX4Z5O_F6Ge3e4oP0uM1WbaYBXTzvv6CBrSiDfQFmELzWn-B2y53PaG9EI2lhNQMoEmoD6EXJMViFbkWEe0FF5M7at_8VMMh9Wqpf1lelvKxL6uK0FFUUlgzvGKZmXvQIAYtdV3giCbqvAi3zel8-zBldkFetrHy5cMezx_g5PMhdwnWQWrE-kcKW5fsueDp2nJobbpNIodk-KQ4CNoKm_520V9sDoJs6g-LRiNT6NaE3NPt8YuQrnBBYNmT-_Ot0QS-fF0COeW50QjRMvANJx5gFJEci0aGlzxwYNH-u16UwqzFzLhnLAN8NxOjaMe5vxQCSa37YJeF-FYrDi5UrUH54eiHDTL5zQRbXVw723OqVjCpWMaMjOnOk1IvQoyCqXio1UFrgAz3j-EkbQ_og5Na7WLP2gMkm1qXGzztw_4RY5WShu4GhT-_O2pkc-hKJcuXjFzTHmgy6CE6dCNYH4bZ3EUsBhhA8lsBC1Q1_42NzWmSni0oKEw8mm9VLmqMnMoXI4FdkSa1HpTEOLVlY7sphEYroC5A-e8bH5Y8bjofB-Ht2i2eBR1TtupkR0nn8nR13pstWEf_8CD_dgq0cGTU-_xm1tg8eCM7BnthvkSWklWg0kGb4aj/download)
1. Put downloaded thumos.tar.gz under the path `actionformer_release/data/` and extract it.
2. Delete the thumos.tar.gz

# Train
```python
python ./train.py ./configs/thumos_i3d.yaml --output reproduce
```
Monitor the training with `tensorboard`
```bash
tensorboard --logdir=./ckpt/thumos_i3d_reproduce/logs
```

# Eval with Debugging
The original author use the below command to evaluate the trained ckpt.
```python
python ./eval.py ./configs/thumos_i3d.yaml ./ckpt/thumos_i3d_reproduce
```
To check what happens inside the code, create a `Run/Debug Configuration` for the script `eval.py` and set the predefined `parameters`:
![Screenshot from 2022-04-21 18-30-01](https://user-images.githubusercontent.com/42603768/164439593-092c535b-5798-4fcc-af2b-9d0b192acc0e.png)
Now start the debug to check what exactly happens inside the codes.

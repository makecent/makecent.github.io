---
layout: default
title: Debug
parent: Collections
nav_order: 2
---
1. TOC
{:toc}
# Deep learning Debugging
Implementing deep neural networks (DNNs) can inadvertently introduce bugs that are difficult to detect. **DNNs often fail silently**, meaning they may continue to operate without throwing any errors even in the presence of significant bugs. For instance, the model might not signal any issues even if there's a mistake such as failing to adjust bounding boxes after image flips, or inadvertently mixing up dimensions. The only indications of these issues might be poor performance or model unable to converge. A more insidious consequence is when the model performs *slightly worse* than normal, might making us miss these bugs entirely. Belows are BUGs that happens to me:

## Unable to converge
In this case, there are bugs making the traning not coverge, which often be manifested as a large training loss that does not decrease as iteration increases as expected.

### Batch size too small cause too less positive
**Background:** In this case, I am training a DETR for temporal action detection but the training loss, especially the classification loss, retain large and does not coverge.

**Reason:** In detection models, there are often a assigning/matching operation to assign/match the ground truth to reference points (*anchors* of anchor-based, *points* of anchor-free, and *queries* of DETR-like). For a DETR-like model, the matching is especially difficult because the initial predicitons are random (a set of random initialized learnable queires input to the decoder). Moreover, the training of problem-free DETR-like models is already known to be unstable and hard. Thus, when the batch size is too small, it's very likely that there is not a single one good matching inside a batch, e.g. the IoUs between all predictions and GT are zeros, which could cause the training has a terrible beggining that is too information-less to optimize the model towards a good direction.

**Solution:** Increase the batch size (from 1 to 8) making the training from not converge at all to converge. BTW, I noticed that removing the encoder entirely could benifit the training loss and validation metric, and allow me to use a very large batch-size, which however may only applied to my specific case.

**Conclude:** The **batch size** is important for some tasks and models, at least for the DETR models.

# Python Debugging
## Error caused by the edited mutable Dictionary
Avoid editing the dictionary because when programming projects deep learning, the dictionary is very likely to be REUSED in next epochs. Editing a deep copy of the original dictionary instead (`dict_ = copy.deepcopy(dict)`).

# Pytorch Debugging
## RuntimeError: CUDA error: device-side assert triggered
Normally because index out of tensor dimension, frenquently happens to wrong label values.
Add `CUDA_LAUNCH_BLOCKING=1 python ...` to get better tracing about the position where bug happens. Somtimes together with move the tensor to cpu can give you more details

## NCCL communicator was aborted on rank 1
This error could be caused by many reasons but is NOT common. My case is quite special: it starts from that my model has some parameters that only contribute to the loss when input statisfying some condition, leading to the `error of not contributing to the loss`. So I tried forcing the loss to zero when parameters not contributing to the loss, and I also tried seeking help from setting `find_unused_parameters=True`. Both can make me get rid of the `error of not contriting to the loss`, but resulting the `error of broken NCCL conmunication`. Finally, I solved this error by making all parameters always contribute to the loss.

In short, it seems that it's OK for model to have some parameters not participating the computation of the training loss (we can solve it by setting `find_unused_parameters=True`). But if these parameters are sometimes used while sometimes not in the iterations, it will cause the NCCL communication failed.



## First GPU memory higher than the other
When multiple GPU distributed training, I was once found that the rank0 GPU take too much memory than the others, which limits my batch size choice:

![Screenshot from 2022-07-19 23-18-58](https://user-images.githubusercontent.com/42603768/179787093-64e697a8-4baf-4378-9131-7cfe568ce0eb.png)

I finally found solution from [here](https://discuss.pytorch.org/t/multiple-gpu-use-significant-first-gpu-memory-consumption/48846/7?u=chongkai_lu).

By revising my code from
```python
from mmcv.runner import _load_checkpoint, load_state_dict

class MViT2(torch.nn.Module):
    def __init__(self):
        super().__init__()
        state_dict = _load_checkpoint("path_to.pth")
        load_state_dict(model, state_dict['model_state'])
```
to 
```python
from mmcv.runner import _load_checkpoint, load_state_dict

class MViT2(torch.nn.Module):
    def __init__(self):
        super().__init__()
        state_dict = _load_checkpoint("path_to.pth", map_location=lambda storage, loc: storage)
        load_state_dict(model, state_dict['model_state'])
```
Problem solved:

![Screenshot from 2022-07-19 23-21-52](https://user-images.githubusercontent.com/42603768/179787709-4e170813-9e42-404a-8c27-afbb777e7ca4.png)

### Multiprocessing slow
My instance is special: my multiprocessing task is at validation stage for computing the evluation metric. **It runs fast during the training, but slow when soly evaluating the results**. 

Specifically, when I run a complete training epoch, the validation is fast. While when I manually load the saved model outputs, and testing them using the same evaluation script, it's very slow (0.3 vs 1.5). 

I found the difference is that my lib (mmaction2) setup [multi-processing environment variables](https://github.com/open-mmlab/mmaction2/blob/fa3221f23168f8e1d964e3d56b0af7d7861a03d2/mmaction/utils/setup_env.py#L30) which my manual evalution script does NOT. After adding the python code below into my `evaluation.py`, the problem is solved.
```python
import os
if 'OMP_NUM_THREADS' not in os.environ:
    os.environ['OMP_NUM_THREADS'] = str(1)
if 'MKL_NUM_THREADS' not in os.environ:
    os.environ['MKL_NUM_THREADS'] = str(1)
```

## G++ version probelm
```terminal
RuntimeError: The current installed version of g++ (8.4.0) is greater than the maximum required version by CUDA 10.2 (8.0.0). Please make sure to use an adequate version of g++ (>=5.0.0, <=8.0.0).
```
Soution:
```terminal
# sudo apt install g++-7
CXX=/usr/bin/g++-7 yourcommand
```
Of course you have to install g++-7 first if there is no g++-7 in the path.

## Pytorch version is different with the installed
My case is that the version showed by `torch.__version__` is different with the torch version shown in `conda list`. No matter how many times I reinstall the pytorch and even re-create the environment, the `torch.__version__` is always `2.0`, while every time I am sure that I just have run `conda install pytorch==1.5.1`.

The problem is that the new environment will use the system Pyhhon if no python version was specified when it was created:
```terminal
conda create -n test
conda activate test
which python
# /use/bin/python
pip list
# ...
# torch 2.0.1
# ...
```
So it will always use the torch installed in the system rather than the environment.

**Solution: create env with specific Python version**:
```terminal
conda create -n test python=3.7
conda activate test
which python
# /home/louis/miniconda3/envs/test/bin/python
pip list
# empty
```
*It may be related to the operation `conda config --set auto_activate_base false`. I reinstall conda and set auto_activate_base to false, then encounter this problem.

## Conda error on windows system
Each time I open a terminal there will pop up below error:
```terminal
WindowsPowerShell\profile.ps1 cannot be loaded because running scripts is disabled on this system. For more infor
```
The reulst is that I cannot switch and install packages in environments correctly.

Solution:
```terminal
conda init
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```
## Parameter not participating loss computation error
This happens on distributed training:
```terminal
RuntimeError: Expected to have finished reduction in the prior iteration before starting a new one. This error indicates that your module has parameters that were not used in producing loss ...
```
Like the message says, there are parameters in your module that don't contribute to the loss and this is not allowed in distribute training.
To find which specific parameters that don't contribute to the loss, set `TORCH_DISTRIBUTED_DEBUG=DETAIL`, e.g.:
```terminal
TORCH_DISTRIBUTED_DEBUG=DETAIL python train.py
```
After finding `parameters` that do not contribute to the loss via `TORCH_DISTRIBUTED_DEBUG=DETAIL`.There are serveral solutions:

- If you do not need them, just delete them
- If you need use them, you may convert them to non-parameter tensors and register them to the module.
- You may also set `require_grad=False` to these parameters **BEFORE** you wrap the model to distribtued.
- Pass`find_unused_parameters=True` into `torch.nn.parallel.DistributedDataParallel()` when wrap the model. But it could significantly increase the training time. **Not recommended**
- Keep gradients of these parameters to be zeros via register a customized hook, you may refer to codes from [here](https://chongkai.site/docs/collections/pytorch_practice/#fixing-the-paramter-gradient-using-the-register_hook-function).

# Latex Debugging

## `subcaption` conflict with `subfigure`
```terminal
Missing number, treated as zero.

 
‪./ChapFive.tex, 216‬
<to be read again> 
                   }
l.216     \begin{subtable}[t]{0.335\linewidth}
                                              
A number should have been here; I inserted `0'.
(If you can't figure out why I needed to see a number,
look up `weird error' in the index to The TeXbook.)
Illegal unit of measure (pt inserted).

 
‪./ChapFive.tex, 216‬
<to be read again> 
                   }
l.216     \begin{subtable}[t]{0.335\linewidth}
                                              
Dimensions can be in units of em, ex, in, pt, pc,
cm, mm, dd, cc, nd, nc, bp, or sp; but yours is a new one!
I'll assume that you meant to say pt, for printer's points.
To recover gracefully from this error, it's best to
delete the erroneous units; e.g., type `2' to delete
two letters. (See Chapter 27 of The TeXbook.)
```
To use `subtable` you need `\usepackage{subcaption}` which is conflict with `\usepackage{subfigure}`. Comment the `subfigure` to use `subtable` correctly.

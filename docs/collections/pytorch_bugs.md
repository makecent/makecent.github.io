---
layout: default
title: Debug
parent: Collections
nav_order: 2
---


### RuntimeError: CUDA error: device-side assert triggered
Normally because index out of tensor dimension, frenquently happens to wrong label values.
Add `CUDA_LAUNCH_BLOCKING=1 python ...` to get better tracing about the position where bug happens. Somtimes together with move the tensor to cpu can give you more details

### NCCL communicator was aborted on rank 1
This error could be caused by many reasons but is NOT common. My case is quite special: it starts from that my model has some parameters that only contribute to the loss when input statisfying some condition, leading to the `error of not contributing to the loss`. So I tried forcing the loss to zero when parameters not contributing to the loss, and I also tried seeking help from setting `find_unused_parameters=True`. Both can make me get rid of the `error of not contriting to the loss`, but resulting the `error of broken NCCL conmunication`. Finally, I solved this error by making all parameters always contribute to the loss.

In short, it seems that it's OK for model to have some parameters not participating the computation of the training loss (we can solve it by setting `find_unused_parameters=True`). But if these parameters are sometimes used while sometimes not in the iterations, it will cause the NCCL communication failed.

### Error caused by the edited mutable Dictionary
Avoid editing the dictionary because when programming projects deep learning, the dictionary is very likely to be REUSED in next epochs. Editing a deep copy of the original dictionary instead (`dict_ = copy.deepcopy(dict)`).

### First GPU memory higher than the other
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

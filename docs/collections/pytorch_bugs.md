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

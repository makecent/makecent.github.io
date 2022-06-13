---
layout: default
title: Pytorch Debugging
parent: Collections
nav_order: 2
---


### RuntimeError: CUDA error: device-side assert triggered
Normally because index out of tensor dimension, frenquently happens to wrong label values.
Add `CUDA_LAUNCH_BLOCKING=1 python ...` to get better tracing about the position where bug happens. Somtimes together with move the tensor to cpu can give you more details

### NCCL communicator was aborted on rank 1
This error could be caused by many reasons but is NOT common. In my case, it's because that I manually set the loss to zero when some condition is statisfied (I was hoping this can help me get rid of the need of setting `find_unused_parameters=True`).

### Error caused by the edited mutable Dictionary
Avoid editing the dictionary because when programming projects deep learning, the dictionary is very likely to be REUSED in next epochs. Editing a deep copy of the original dictionary instead (`dict_ = copy.deepcopy(dict)`).

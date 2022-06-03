---
layout: default
title: Training
parent: Collections
nav_order: 7
---
1. TOC
{:toc}
 
# Optimizer setting

The optimizer and schedule used by the famous team should be good because their settings must be based on sufficient grid search. For example, I have been keep following the setting used by:
- [Christoph Feichtenhofer](https://feichtenhofer.github.io/) and his team of the Facebook AI Research (FAIR). Their github repositories [pytorchvideo](https://github.com/facebookresearch/pytorchvideo/) and the [SlowFast](https://github.com/facebookresearch/SlowFast)
- [Wang liming](https://wanglimin.github.io/) of Multimedia Computing Group, Nanjing University. Their github repositories [MCG-NJU](https://github.com/MCG-NJU)

However, I don't have much computational resource compared with the FAIR. Therefore, I also follows some settings that use fewer training epochs and smaller batch size. 

## Up-to-date collections (2022-05-07):

### [MaskFeat (slowfast)](https://arxiv.org/abs/2112.09133)
![Screenshot from 2022-04-26 17-28-41](https://user-images.githubusercontent.com/42603768/165269127-7e93bf07-f7a4-4259-a8f4-780e5a41877f.png)

- Cosine decay does *NOT* include restart, and it ends with `0.01 x base_lr`
- Warmup starts from `0.01 x base_lr`
- It seems that all runs in slowfast use `max_norm=1.0` for clipping the grads, e.g. MViT has [`CLIP_GRAD_L2NORM: 1.0`](https://github.com/facebookresearch/SlowFast/blob/52fb753f8f703b306896afc5613978db0c3c6695/configs/Kinetics/MVIT_B_16x4_CONV.yaml#L63) in its config, which [points](https://github.com/facebookresearch/SlowFast/blob/99a655bd533d7fddd7f79509e3dfaae811767b5c/tools/train_net.py#L169) to [torch.nn.utils.clip_grad_norm_](https://pytorch.org/docs/stable/generated/torch.nn.utils.clip_grad_norm_.html). However, the `max_norm` used by `mmaction2` normally is 40/20.

### [VideoSwin](https://arxiv.org/abs/2106.13230):
![Screenshot from 2022-05-07 13-35-38](https://user-images.githubusercontent.com/42603768/167240101-669e07d4-c2a0-477e-b438-7fbfaa9d7bd1.png)


### My current pratice:
**30 epochs:**
```python
# optimizer
optimizer = dict(type='AdamW', lr=3e-4, weight_decay=0.01)  # decay=0.05 if very large backbone
# optimizer = dict(type='AdamW', lr=3e-4, paramwise_cfg=dict(custom_keys={'backbone': dict(lr_mult=0.1)})) # if pretrained backbone
optimizer_config = dict(grad_clip=dict(max_norm=40)) # max_norm=1.0 if very large backbone
# learning policy
lr_config = dict(policy='CosineAnnealing',
                 min_lr_ratio=0.01,
                 warmup='linear',
                 warmup_ratio=0.01,
                 warmup_iters=2.5,
                 warmup_by_epoch=True)
total_epochs = 30

data = dict(
    videos_per_gpu=32, # batch size=32x2=64, where 2 is the number of gpus 
    ...
```
**50 epochs:**
```python
# optimizer
optimizer = dict(
    type='SGD',
    lr=1e-3,
    momentum=0.9,
    weight_decay=0.0001)
optimizer_config = dict(grad_clip=dict(max_norm=40, norm_type=2))
# learning policy
lr_config = dict(policy='step', step=[20, 40]) # or [10, 20] for 25 poech 
total_epochs = 50
```
**200 epochs**
```python
# optimizer
optimizer = dict(type='AdamW', lr=1e-4, weight_decay=0.05)
optimizer_config = dict(grad_clip=dict(max_norm=1.0))
# learning policy
lr_config = dict(policy='CosineAnnealing',
                 min_lr_ratio=0.01,
                 warmup='linear',
                 warmup_ratio=0.01,
                 warmup_iters=20,
                 warmup_by_epoch=True)
total_epochs = 200

data = dict(
    videos_per_gpu=16, # batch size 16 * 2 = 32, where 2 is the number of gpus 
    ...
```

Tips:
- There is a known [linear scaling rule](https://arxiv.org/abs/1706.02677) -- *When the minibatch size is multiplied by k, multiply the learning rate by k.*
- Warmup seems to be impactful when the backbone network is Transformer ([ActionFormer](https://arxiv.org/abs/2202.07925), [Liyuan Liu 2020](https://arxiv.org/abs/2004.08249)).

# Params and FLOPs
> Flop is not a well-defined concept.

Results comparison:

| X3D-M | fvcore | torchinfo | thop | official |
|:-------|:------|:------|:------|:-----|
| params (M) | 3.79 | 3.79 | 3.79 | 3.8 |
| FLOPs (G)  | 5.14 | 4.73 | 4.90 | 6.2 |

| ResNet50 | fvcore | torchinfo | thop | official |
|:-------|:------|:------|:------|:-----|
| params (M) | 25.56 | 25.56 | 25.56 | 25.6 |
| FLOPs (G)  | 4.14 | 4.09 | 4.11 | 3.8 |

## [fvcore](https://github.com/facebookresearch/fvcore)
> FAIR is responsible for maintaining this library.

Installation:
```shell
pip install -U fvcore
```

Example 1:
```python
import torch
from fvcore.nn import FlopCountAnalysis, parameter_count

imgs = torch.randn(1, 3, 16, 224, 224)
model = torch.hub.load("facebookresearch/pytorchvideo", model="x3d_m", pretrained=False)

flops = FlopCountAnalysis(model, imgs)
print(flops.total())

params = parameter_count(model)
print(params[''])
```

FLOPs Output:
```shell
Using cache found in /home/louis/.cache/torch/hub/facebookresearch_pytorchvideo_main
Unsupported operator aten::add_ encountered 83 time(s)
Unsupported operator aten::mean encountered 15 time(s)
Unsupported operator aten::sigmoid encountered 15 time(s)
Unsupported operator aten::mul encountered 15 time(s)
Unsupported operator prim::PythonOp.SwishFunction encountered 26 time(s)
Unsupported operator aten::add encountered 26 time(s)
Unsupported operator aten::avg_pool3d encountered 1 time(s)
Unsupported operator aten::softmax encountered 1 time(s)
Unsupported operator aten::adaptive_avg_pool3d encountered 1 time(s)
5141904896
3794274
```

Example 2 (mmlab-style):
```python
import torch
from fvcore.nn import FlopCountAnalysis, parameter_count, flop_count_table
from mmcv import Config
from mmaction.models.builder import build_model, build_backbone

inputs = torch.randn(1, 3, 16, 224, 224)
model = build_backbone(Config.fromfile("configs/mvit/mvit_16x4_kinetics400_video.py").model.backbone)

# inputs = (torch.randn(1, 1, 3, 16, 224, 224), torch.randint(10, (1, 1)))
# model = build_model(Config.fromfile("configs/mvit/mvit_16x4_kinetics400_video.py").model)

flops = FlopCountAnalysis(model, inputs)
print(flop_count_table(flops))
params = parameter_count(model)

print(f"FLOPS:\t{flops.total()}")
print(f"Params:\t{params['']}")
```

More details can be found in this [docs](https://github.com/facebookresearch/fvcore/blob/main/docs/flop_count.md), API of [FlopCountAnalysis](https://detectron2.readthedocs.io/en/latest/modules/fvcore.html#fvcore.nn.FlopCountAnalysis), API of [parameter_count](https://detectron2.readthedocs.io/en/latest/modules/fvcore.html#fvcore.nn.parameter_count)

## [torchinfo](https://github.com/TylerYep/torchinfo)
> mimc the tensorflow summary 

Installation:
```shell
pip install torchinfo
```

Example:
```python
import torch
from torchinfo import summary
model = torch.hub.load("facebookresearch/pytorchvideo", model="x3d_m", pretrained=False)
summary(model, input_size=(1, 3, 16, 224, 224))
```

Output:
```shell
==============================================================================================================
Layer (type:depth-idx)                                       Output Shape              Param #
==============================================================================================================
Net                                                          --                        --
├─ModuleList: 1-1                                            --                        --
│    └─ResStage: 2                                           --                        --
│    │    └─ModuleList: 3-1                                  --                        15,370
│    └─ResStage: 2                                           --                        --
│    │    └─ModuleList: 3-2                                  --                        73,248
│    └─ResStage: 2                                           --                        --
│    │    └─ModuleList: 3-3                                  --                        569,256
│    └─ResStage: 2                                           --                        --
│    │    └─ModuleList: 3-4                                  --                        1,347,440
│    └─ResNetBasicStem: 2-1                                  [1, 24, 16, 112, 112]     --
│    │    └─Conv2plus1d: 3-5                                 [1, 24, 16, 112, 112]     768
│    │    └─BatchNorm3d: 3-6                                 [1, 24, 16, 112, 112]     48
│    │    └─ReLU: 3-7                                        [1, 24, 16, 112, 112]     --
│    └─ResStage: 2-2                                         [1, 24, 16, 56, 56]       --
│    └─ResStage: 2-3                                         [1, 48, 16, 28, 28]       --
│    └─ResStage: 2-4                                         [1, 96, 16, 14, 14]       --
│    └─ResStage: 2-5                                         [1, 192, 16, 7, 7]        --
│    └─ResNetBasicHead: 2-6                                  [1, 400]                  --
│    │    └─ProjectedPool: 3-8                               [1, 2048, 1, 1, 1]        968,544
│    │    └─Dropout: 3-9                                     [1, 2048, 1, 1, 1]        --
│    │    └─Linear: 3-10                                     [1, 1, 1, 1, 400]         819,600
│    │    └─Softmax: 3-11                                    [1, 400, 1, 1, 1]         --
│    │    └─AdaptiveAvgPool3d: 3-12                          [1, 400, 1, 1, 1]         --
==============================================================================================================
Total params: 3,794,274
Trainable params: 3,794,274
Non-trainable params: 0
Total mult-adds (G): 4.73
==============================================================================================================
Input size (MB): 9.63
Forward/backward pass size (MB): 1358.41
Params size (MB): 15.18
Estimated Total Size (MB): 1383.22
==============================================================================================================
```

## [thop](https://github.com/Lyken17/pytorch-OpCounter)
> Simple, 3.4k stars

Installation:
```shell
pip install thop
```

Example:
```python
import torch
from thop import profile
imgs = torch.randn(1, 3, 16, 224, 224)
model = torch.hub.load("facebookresearch/pytorchvideo", model="x3d_m", pretrained=False)
macs, params = profile(model, inputs=(imgs, ))
print(macs, params)
```

Output:
```shell
[INFO] Register count_convNd() for <class 'torch.nn.modules.conv.Conv3d'>.
[WARN] Cannot find rule for <class 'pytorchvideo.layers.convolutions.Conv2plus1d'>. Treat it as zero Macs and zero Params.
[INFO] Register count_bn() for <class 'torch.nn.modules.batchnorm.BatchNorm3d'>.
[INFO] Register zero_ops() for <class 'torch.nn.modules.activation.ReLU'>.
[WARN] Cannot find rule for <class 'pytorchvideo.models.stem.ResNetBasicStem'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'torch.nn.modules.activation.Sigmoid'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'torch.nn.modules.container.Sequential'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'fvcore.nn.squeeze_excitation.SqueezeExcitation'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.layers.swish.Swish'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.models.resnet.BottleneckBlock'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.models.resnet.ResBlock'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'torch.nn.modules.linear.Identity'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'torch.nn.modules.container.ModuleList'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.models.resnet.ResStage'>. Treat it as zero Macs and zero Params.
[INFO] Register count_avgpool() for <class 'torch.nn.modules.pooling.AvgPool3d'>.
[WARN] Cannot find rule for <class 'pytorchvideo.models.x3d.ProjectedPool'>. Treat it as zero Macs and zero Params.
[INFO] Register zero_ops() for <class 'torch.nn.modules.dropout.Dropout'>.
[INFO] Register count_linear() for <class 'torch.nn.modules.linear.Linear'>.
[WARN] Cannot find rule for <class 'torch.nn.modules.activation.Softmax'>. Treat it as zero Macs and zero Params.
[INFO] Register count_adap_avgpool() for <class 'torch.nn.modules.pooling.AdaptiveAvgPool3d'>.
[WARN] Cannot find rule for <class 'pytorchvideo.models.head.ResNetBasicHead'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.models.net.Net'>. Treat it as zero Macs and zero Params.
4896248152.0 3794274.0
```

# Loss quantization

## Cross Entropy

![image](https://user-images.githubusercontent.com/42603768/168471163-3998315d-704e-4518-acf3-c129213a2d3e.png)

| Prob. of GT |  Loss       | Comment                            |
|:-----------|:------------|:-----------------------------------|
| 0          |   100       |                                    |
| 0.01       |   **4.61**  | :disappointed: exploding gradient? |
| 0.1        |   2.30      |                                    |
| 0.2        |   1.61      |                                    |
| 0.3        |   1.20      |                                    |
| 0.4        |   0.92      |                                    |
| 0.5        |   **0.69**  | :neutral_face: meaningful          |
| 0.6        |   0.51      | :flushed: it works                 |
| 0.7        |   **0.36**  | :grinning: good                    |
| 0.8        |   0.22      | :wink: great                       |
| 0.9        |   **0.11**  | :relaxed: pretty good              |
| 0.95       |   0.05      |                                    |
| 0.99       |   **0.01**  | :triumph: perfect                  |




## GIoU
Copied from https://giou.stanford.edu/:

![image](https://user-images.githubusercontent.com/42603768/168458537-b95067ce-2b54-4ed3-a7dd-73572ca300a8.png)

- Note that GIoU and IoU do not one-to-one map to each other. 
- The brown area are pair-samples overlapping with each other. 
- Normally the GIoU is close to the IoU when the IoU is close to 1 **or** when the overlapping samples have similar x/y postion, otherwise the GIoU is smaller than the IoU.
- GIoU_loss equals `1 - GIoU` 


# Miscellaneous
## backbone memory record
```
input_size=4x16x4x224x224
is_training=True
include cls_head
```

|       | Source | Params (M) | Memory (M) |
|:------|:-----|:-----|:-----|
| X3D-M | pytorchvideo | 3.79  | 5660 |
| X3D-M | mmaction2 | 3.79 | 6382 |
| I3D  | mmaction2 | 28.04 | 3601 |
| I3D  | [original](https://github.com/hassony2/kinetics_i3d_pytorch) | 12.70 |  3815 | 
| Slowonly | mmaction2 | -  | 3601 |
| MViT-B | pytorchvideo | 36.61 | 10044 |

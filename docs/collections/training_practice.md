---
layout: default
title: Training
parent: Collections
nav_order: 7
---
1. TOC
{:toc}

# Loss quantization
## Binary Cross Entropy
Assume Label is **1**.

| Prediction |  Loss       | Comment                            |
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
 
# Optimizer setting

I decide to follow the training setting used by the famous team, specifically the Facebook AI Research. More precisely, the [pytorchvideo](https://github.com/facebookresearch/pytorchvideo/), also known as the [SlowFast](https://github.com/facebookresearch/SlowFast) lead by the [Christoph Feichtenhofer](https://feichtenhofer.github.io/). 

Accoring to the recently published papers, [**AdamW**](https://pytorch.org/docs/stable/generated/torch.optim.AdamW.html#torch.optim.AdamW) optimizer maybe a good choice. The detailed setting of [MaskFeat](https://arxiv.org/abs/2112.09133) is:
![Screenshot from 2022-04-26 17-28-41](https://user-images.githubusercontent.com/42603768/165269127-7e93bf07-f7a4-4259-a8f4-780e5a41877f.png)

However, I don't have much computation resource compared with the FAIR. It limits the range of my training setting, e.g., the batch size and epochs. The cost of grid search of hyper-parameter is too high for me. Therefore, I only adopt the settings from FAIR that are relatively more impactful and not depend on heavy engenerring. 

My current settings:

Training a X3D-M on Kinetics400 (optimal):
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

Training a X3D-M on Kinetics400 (fast):
```python
# optimizer
optimizer = dict(type='Adam', lr=1e-4)
optimizer_config = dict(grad_clip=None)
# learning policy
lr_config = dict(policy='fixed')
total_epochs = 20

data = dict(
    videos_per_gpu=16, # batch size 16 * 2 = 32, where 2 is the number of gpus 
    ...
```

Notes:
- There is a known [linear scaling rule](https://arxiv.org/abs/1706.02677) -- *When the minibatch size is multiplied by k, multiply the learning rate by k.*
- Warmup seems to be impactful when the backbone network is Transformer ([ActionFormer](https://arxiv.org/abs/2202.07925), [Liyuan Liu 2020](https://arxiv.org/abs/2004.08249)).

# Params and FLOPs
> Flop is not a well-defined concept.

Results comparison:
| X3D | fvcore | torchinfo | thop | official |
|:-------|:------|:------|:------|:-----|
| params | 3.79M | 3.79M | 3.79M | 3.8M |
| FLOPs  | 5.14G | 4.73G | 4.90G | 6.2G |

| ResNet50 | fvcore | torchinfo | thop | official |
|:-------|:------|:------|:------|:-----|
| params | 25.56M | 25.56M | 25.56M | 25.6M |
| FLOPs  | 4.14G | 4.09G | 4.11G | 3.8G |

## [fvcore](https://github.com/facebookresearch/fvcore)
> FAIR is responsible for maintaining this library.

Installation:
```shell
pip install -U fvcore
```

Example:
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

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


## Position of Normalization Layers
> [1] [On Layer Normalization in the Transformer Architecture](https://arxiv.org/pdf/2002.04745.pdf)
> [2] [A ConvNet for the 2020s](https://arxiv.org/pdf/2201.03545.pdf)
> [3] [Understanding the Difficulty of Training Transformers](https://arxiv.org/pdf/2004.08249.pdf)
> [4] [RealFormer: Transformer Likes Residual Attention](https://arxiv.org/pdf/2012.11747.pdf)
> [5] [Blog](https://kexue.fm/archives/9009)

Let's start with the Transformer. There are two kinds of known choices for the location of normalization: Post-LN and Pre-LN. Pre-LN is proposed newly than the Post-LN.
![Screenshot from 2022-06-30 13-34-20](https://user-images.githubusercontent.com/42603768/176600503-613d0672-b584-44cd-bf91-ea3f5fe507b7.png)

Conclusion:
- Post-LN is relied and sensitive to the Warm-Up[1]， because *"the Post-LN Transformer cannot be trained with a large learning rate from scratch"*[1].
- Pre-LN is NOT sensitive to the Warm-Up, and *"Pre-LN Transformer converges faster than the Post-LN Transformer"*[1].
- Post-LN is NOT as stable as Pre-LN, but it *optimal* performance is better[3][4][5].
- Pre-LN is recommended for easy training, while Post-LN is recommended when high performance is the target, at the cost of manul engineering, e.g. the Admin initilization [3] and the Warm-up.

What about CNN? Below is the block structure of the latest ConvNext:

![Screenshot from 2022-06-30 13-52-23](https://user-images.githubusercontent.com/42603768/176603032-6aeaf99a-0ec0-4b4c-a2c3-64893daae5fa.png)

Conclusion:
- The widely used Batch-Normalization layers are now replaced by the Layer-Normalization layers. BTW, the amount is reduced.
- The Pre-LN is recommended.

## Video backbone benchmark

|        | Source | ~~top1~~ | Params (M) | GFLOPs | Memory (M) | Training speed | Testing speed |
|-------|-----|-----|-----|-----|-----|-----|-----|
| I3D  | [original](https://github.com/hassony2/kinetics_i3d_pytorch) | ~~71.1%~~ | 12.29 | 27.90 |  1963 | 35.6 iter/s | 86.8 iter/s |
| I3D  | mmaction2 | ~~73.3%~~ |27.22 | 16.74 | 2175  | 16.6 iter/s | 89.3 iter/s |
| X3D-M | pytorchvideo | ~~76.2%~~ | 3.79 | 5.15 |  - | - | - |
| X3D-M | mmaction2 | ~~75.6%~~ | 3.79 | 5.15 |  2159  | 23.0 iter/s | 60.0 iter/s |
| MViT-B | pytorchvideo | ~~80.2%~~ | 36.30 | 70.8 | 3231 | 16.2 iter/s | 55.5 iter/s |

- top1 is reported on the kinetics400 validation set but are directly **coied** from the paper/repo. The original work use different temporal and spatial resolution and training/testing data augmentation, thereby the top1 here is **just for reference**. It makes NO sense to consider together the top1 and the other variables. While generally speaking, the lower models are newer and *should* have higher best accuracy.
- Params and GFLOPs only calculate the backbone, while speed and memory involves the head.
- Input (N C T H W) is of shape (1, 3, 16, 224, 224).
- Params and GFLOPs are computed using the fvcore lib.
- Speed and memory evaluation are conducted on a single 2080ti GPU.

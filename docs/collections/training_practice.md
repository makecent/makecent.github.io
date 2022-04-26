---
layout: default
title: Training Practices
parent: Collections
nav_order: 7
---
1. TOC
{:toc}

# Loss quantization
## Binary Cross Entropy
Assume Label is **1**.
|Prediction |  Loss       | Commetns  |
|-----------|-------------|------------|
|0          |   100       | |
|0.01       |   **4.61**  | :disappointed: exploding gradient? |
|0.1        |   2.30      | |
|0.2        |   1.61      | |
|0.3        |   1.20      | |
|0.4        |   0.92      | |
|0.5        |   **0.69**  | :neutral_face: meaningful |
|0.6        |   0.51      | :flushed: it works |
|0.7        |   **0.36**  | :grinning: good |
|0.8        |   0.22      | :wink: great|
|0.9        |   **0.11**  |:relaxed: pretty good |
|0.95       |   0.05      | |
|0.99       |   **0.01**  |:triumph: (perfect) |
 
# Optimizer setting

I decide to follow the training setting used by the famous team, specifically the Facebook AI Research. More precisely, the [pytorchvideo](https://github.com/facebookresearch/pytorchvideo/), also known as the [SlowFast](https://github.com/facebookresearch/SlowFast) lead by the [Christoph Feichtenhofer](https://feichtenhofer.github.io/). 

Accoring to the recently published papers, [**AdamW**](https://pytorch.org/docs/stable/generated/torch.optim.AdamW.html#torch.optim.AdamW) optimizer maybe a good choice. The detailed setting of [MaskFeat](https://arxiv.org/abs/2112.09133) is:
![Screenshot from 2022-04-26 17-28-41](https://user-images.githubusercontent.com/42603768/165269127-7e93bf07-f7a4-4259-a8f4-780e5a41877f.png)

However, I don't have much computation resource compared with the FAIR. It limits the range of my training setting, e.g., the batch size and epochs. The cost of grid search of hyper-parameter is too high for me. Therefore, I only adopt the settings from FAIR that are relatively more impactful and not depend on heavy engenerring. My current setting for training a X3D-M on Kinetics400:
```python
# optimizer
optimizer = dict(type='AdamW', lr=1e-4, weight_decay=0.05, )
optimizer_config = dict(grad_clip=None)

# learning policy
lr_config = dict(policy='CosineRestart', periods=[5, 10, 20], min_lr_ratio=0.1,
                 warmup='linear',
                 warmup_iters=5,
                 warmup_by_epoch=True)
total_epochs = 40

data = dict(
    videos_per_gpu=16, # batch size 16 * 2 = 32, where 2 is the number of gpus 
    ...
```
Notes:
- There is a known [linear scaling rule](https://arxiv.org/abs/1706.02677) -- *When the minibatch size is multiplied by k, multiply the learning rate by k.*
- Warmup seems to be impactful when the backbone network is Transformer ([ActionFormer](https://arxiv.org/abs/2202.07925), [Liyuan Liu 2020](https://arxiv.org/abs/2004.08249)).

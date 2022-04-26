---
title: Useful links for using mmaction2
parent: Posts
---
1. TOC
{:toc}

# Config varibales
Here we do NOT expalin the meaning of variables in the config, which we refer the reader to the [official document](https://github.com/open-mmlab/mmaction2/blob/master/docs/tutorials/1_config.md). Instead, we go deep into the **arguments of config variables**, e.g. `by_epoch` of variables `evaluation`

## [`evalution`](https://github.com/open-mmlab/mmaction2/blob/c87482f6b53e839bc00506d474b38f797db0fd8f/mmaction/core/evaluation/eval_hooks.py#L45)
Example in config:
```python
evaluation = dict(interval=5, metrics=['top_k_accuracy', 'mean_class_accuracy'])
```
Description:
```python
This hook will regularly perform evaluation on validation dataset in a given interval.
```
**Arguments:**
```python
    start (int | None, optional): Evaluation starting epoch. It enables
        evaluation before the training starts if ``start`` <= the
        resuming epoch. If None, whether to evaluate is merely decided
        by ``interval``. Default: None.
    interval (int): Evaluation interval. Default: 1.
    by_epoch (bool): Determine perform evaluation by epoch or by
        iteration. If set to True, it will perform by epoch.
        Otherwise, by iteration. default: True.
    save_best (str | None, optional): If a metric is specified, it
        would measure the best checkpoint during evaluation. The
        information about best checkpoint would be save in best.json.
        Options are the evaluation metrics to the test dataset. e.g.,
         ``top1_acc``, ``top5_acc``, ``mean_class_accuracy``,
        ``mean_average_precision``, ``mmit_mean_average_precision``
        for action recognition dataset (RawframeDataset and
        VideoDataset). ``AR@AN``, ``auc`` for action localization
        dataset. (ActivityNetDataset). ``mAP@0.5IOU`` for
        spatio-temporal action detection dataset (AVADataset).
        If ``save_best`` is ``auto``, the first key of the returned
        ``OrderedDict`` result will be used. Default: 'auto'.
    rule (str | None, optional): Comparison rule for best score.
        If set to None, it will infer a reasonable rule. Keys such as
        'acc', 'top' .etc will be inferred by 'greater' rule. Keys
        contain 'loss' will be inferred by 'less' rule. Options are
        'greater', 'less', None. Default: None.
    **eval_kwargs: Evaluation arguments fed into the evaluate function
        of the dataset.
```
**Tips:**

- By setting the `by_epoch=False`, we can conduct validation in a interval of iteration level. This can also be used to quicky enter to the validation phase for debugging purpose.
- The argumetns of function `Dataset.evaluate()` are set in here.

## [`optimizer`](https://github.com/open-mmlab/mmcv/blob/de0c1039f756ef2b29fd357a2a64968497323a86/mmcv/runner/optimizer/default_constructor.py#L13)
Example in config:
```python
optimizer = dict(type='SGD', lr=0.01, momentum=0.9, weight_decay=0.0001)
```

Description:
```python
Configure a optimizer that exists in the `pytorch` pacakge.
```

**Arguments**:
```python
    Positional fields are
        - `type`: class name of the optimizer.
    Optional fields are
        - any arguments of the corresponding optimizer type, e.g.,
          lr, weight_decay, momentum, etc.
    paramwise_cfg (dict, optional): Parameter-wise options.
```

**Tips:**

- It points to the optimizers in the `pytorch`. All available optimizer can be found in the [pytorch optimizer page](https://pytorch.org/docs/stable/optim.html#algorithms).
- You may be interested in my personal [recommended optimizer setting]().
- The `paramwise_cfg` can be used to set different learning rate for different model parts. For example, `paramwise_cfg = dict(custom_keys={'backbone': dict(lr_mult=0.1)})` will used `0.1*lr` for the backbone parameters. But pls noted that the `paramwise_cfg` is not a varibale of the `optimizer` but an independent config variable. `paramwise_cfg` can do more than setting different `lr` for different model layers, and the details can be found in the [source code page](https://github.com/open-mmlab/mmcv/blob/de0c1039f756ef2b29fd357a2a64968497323a86/mmcv/runner/optimizer/default_constructor.py#L13).
# Pipelines

## [`RandomResizedCrop`](https://github.com/open-mmlab/mmaction2/blob/c87482f6b53e839bc00506d474b38f797db0fd8f/mmaction/datasets/pipelines/augmentations.py#L701)

Description:
```python
Random crop that specifics the *area* and *height-weight ratio* range of the **cropped shape**.
```

**Arguments:**
```python
    area_range (Tuple[float]): The candidate area scales range of
        output cropped images. Default: (0.08, 1.0).
    aspect_ratio_range (Tuple[float]): The candidate aspect ratio range of
        output cropped images. Default: (3 / 4, 4 / 3).
    lazy (bool): Determine whether to apply lazy operation. Default: False.
```

**Tips:**

- This data augmentation mimic the `torch.RandomResizedCrop`, which is widly used in image-based tasks, known as the Inception-style random cropping. It first determine the cropping shape, whose aspect ratio and area is randomly selected in the given range. Then with the specific cropping shape, it randomly crop a sub-area from the input.
- This pipeline is normally followed by a `Resize(scale=(224, 224), keep_ratio=False)`. Besides, it normally follows a `Rescale(scale=(-1, 256))`, i.e., rescale the short-side to 256. I suggest to conduct the short-side rescaling *offline* because it is fixed. Offline rescaling can reduce the data augmentation time and sometimes can significantly reduce the disk size used by the datasets, e.g., kinetics400.

## [`PytorchVideoTrans(type='RandomShortSideScale')`](https://pytorchvideo.readthedocs.io/en/latest/_modules/pytorchvideo/transforms/transforms.html#RandomShortSideScale)

Description:
```python
Scale that specifics the mi
```

**Arguments:**
```python
    min_size (int): 256
    max_size (int): 320
```

**Tips:**

- Compared to the `RandomResizedCrop`, it scale the input volume's short side to a randomly selected int from the given range.
- This pipeline is normally followed by a `RandomCrop(size=(224, 224))`.
- For these whose datasets are already rescaled to short-side=256, this pipeline is not a good choice.
- This data augmentation was used by the vgg, non-local, slowfast, x3d. However, the latest paper MViT2, from the same team of x3d and slowfast, now also used the Inception-style cropping, i.e. `RandomResizedCrop`, for data augmention in training.

## [SampleFrames](https://github.com/open-mmlab/mmaction2/blob/01c94d365a429e06ff7515eac73d2a091d9cd513/mmaction/datasets/pipelines/loading.py#L83) and [DenseSampleFrames](https://github.com/open-mmlab/mmaction2/blob/01c94d365a429e06ff7515eac73d2a091d9cd513/mmaction/datasets/pipelines/loading.py#L333)

Description:
0. Let's note the `clip_len`, `frame_interval`, and `num_clips` as (clip_len x frame_interval x clip_len), e.g. (16x4x2)
1. **`SampleFrames(16x4x1)`:** This is the most frequently used, and the most basic sampling strategy. It randomly samples *16* frames with interval *4* from the input video. It can be regarded as first randomly cropping a 64-frame sub-video from the input video, then uniformally sampling 16 frames from the cropped sub-video. The output shaoe is (16, H, W)
2. `SampleFrames(16x4x8)`: Now the `num_clips=8`, this can be regarded as first dividing the input video into *8* sub-videos, then conducting the same operations as the `SampleFrames(16x4x1)` on each sub-video. This example is just for explanation. I have never seen such a combiantion of arguments. The output shape is (256, H, W)
3. **`SampleFrames(1x1x8)`:** Same as the point 2, but in this case we only randomly sample *one* frame in each of the *8* sub-videos. This sampling strategy is what the *TSN* proposes to use to cover global information of the videos. And I found that TSN is the only one using this kind of arguments combination. The output shape is (8, H, W), whereas accoring to the TSN, each of the 8 frames will be seperately fed into a 2D ResNet, i.e., the 8 will be multiplied into the batch_size dimension.
4. `DenseSampleFrames(16x4x2)`: Similar to `SampleFrames`, but in this case it first crop a sub-video of length 64-frames (arg of `DenseSampleFrames` default=64) from the input video, then apply the `SampleFrames(16x4x2)` on the cropped sub-video. When the `num_clips=1`, `DenseSampleFrames` is same as the `SampleFrames`. This pipeline is hardly used in mmaction2 and I have never seen similar sampling strategy in the literatures. Maybe it's a experimental pipeline.

**Arguments:**
```python
    clip_len (int): Frames of each sampled output clip.
    frame_interval (int): Temporal interval of adjacent sampled frames.
        Default: 1.
    num_clips (int): Number of clips to be sampled. Default: 1.
    temporal_jitter (bool): Whether to apply temporal jittering.
        Default: False.
    twice_sample (bool): Whether to use twice sample when testing.
        If set to True, it will sample frames with and without fixed shift,
        which is commonly used for testing in TSM model. Default: False.
    out_of_bound_opt (str): The way to deal with out of bounds frame
        indexes. Available options are 'loop', 'repeat_last'.
        Default: 'loop'.
    test_mode (bool): Store True when building test or validation dataset.
        Default: False.
    start_index (None): This argument is deprecated and moved to dataset
        class (``BaseDataset``, ``VideoDatset``, ``RawframeDataset``, etc),
        see this: https://github.com/open-mmlab/mmaction2/pull/89.
    keep_tail_frames (bool): Whether to keep tail frames when sampling.
        Default: False.
```
**Tips:**
- If `test_mode=True`, then the cropping in each clip happens at the temporal center.
- Don't worry about the arg `num_clips` (just set them to the defaul 1) unless you want to use sampling strategy like the TSN, TSM used.
- `out_of_bound_opt` is used to handle the occasion that the input video is shorter than `clip_len x frame_interval`. Normally it's not important. I mention this just for better understanding.
- For arg `keep_tail_frames`, its motivation can be found in [here](https://github.com/open-mmlab/mmaction2/issues/1048). Don't need care about it if `num_clips=1` 

# [`lr_config`](https://github.com/open-mmlab/mmcv/blob/969e2af866045417dccbc3980422c80d9736d970/mmcv/runner/hooks/lr_updater.py#L9)
Configure how to update the learning rate. Constant lr will be used if no configuration. All available `policy` can be found in [here](https://github.com/open-mmlab/mmcv/blob/969e2af866045417dccbc3980422c80d9736d970/mmcv/runner/hooks/lr_updater.py)
Example in config:
```python
lr_config = dict(policy='step', step=[4, 8], gamma=0.1, warmup='linear', warmup_ratio)  # lr = lr * 0.1 after epoch 4 and 8. No warmup used.
```
**Argments:**
```python
    by_epoch (bool): LR changes epoch by epoch
    warmup (string): Type of warmup used. It can be None(use no warmup),
        'constant', 'linear' or 'exp'
    warmup_iters (int): The number of iterations or epochs that warmup
        lasts
    warmup_ratio (float): LR used at the beginning of warmup equals to
        warmup_ratio * initial_lr
    warmup_by_epoch (bool): When warmup_by_epoch == True, warmup_iters
        means the number of epochs that warmup lasts, otherwise means the
        number of iteration that warmup lasts
```
## [`policy="Step"`](https://github.com/open-mmlab/mmcv/blob/969e2af866045417dccbc3980422c80d9736d970/mmcv/runner/hooks/lr_updater.py#L167)
**args:**
```python
    step (int | list[int]): Step to decay the LR. If an int value is given,
        regard it as the decay interval. If a list is given, decay LR at
        these steps.
    gamma (float, optional): Decay LR ratio. Default: 0.1.
    min_lr (float, optional): Minimum LR value to keep. If LR after decay
        is lower than `min_lr`, it will be clipped to this value. If None
        is given, we don't perform lr clipping. Default: None.
```

## [`policy="CosineAnnealing"`](https://github.com/open-mmlab/mmcv/blob/969e2af866045417dccbc3980422c80d9736d970/mmcv/runner/hooks/lr_updater.py#L258)
Example:
```python
lr_config = dict(policy='CosineAnnealing', min_lr_ratio=0.01)
```

**args:**
```python
    min_lr (float, optional): The minimum lr. Default: None.
    min_lr_ratio (float, optional): The ratio of minimum lr to the base lr.
        Either `min_lr` or `min_lr_ratio` should be specified.
        Default: None.
```
**tips:**
Altough the this lr_updater does not contains the restart, it is more frequently used, maybe because it has less parameters.

## [`policy="CosineRestart"`](https://github.com/open-mmlab/mmcv/blob/969e2af866045417dccbc3980422c80d9736d970/mmcv/runner/hooks/lr_updater.py#L344)
Example:
```python
lr_config = dict(policy='CosineRestart', periods=[5, 10, 15], restart_weights=[1, 1, 0.5], min_lr_ratio=0.1)
```

**args:**
```python
    periods (list[int]): Periods for each cosine anneling cycle.
    restart_weights (list[float], optional): Restart weights at each
        restart iteration. Default: [1].
    min_lr (float, optional): The minimum lr. Default: None.
    min_lr_ratio (float, optional): The ratio of minimum lr to the base lr.
        Either `min_lr` or `min_lr_ratio` should be specified.
        Default: None.
```
This should be the ultra version of cosine annealing according to the [original paper](https://arxiv.org/abs/1608.03983). But it's less used.

I finally find that the formula turns to be the most simple way to understand the cosine annealing, which can be found in [related pytorch pages](https://pytorch.org/docs/stable/generated/torch.optim.lr_scheduler.CosineAnnealingWarmRestarts.html#torch.optim.lr_scheduler.CosineAnnealingWarmRestarts):
![Screenshot from 2022-04-26 19-24-52](https://user-images.githubusercontent.com/42603768/165289721-bca32e51-9eaa-47dd-858c-f1209be40ec0.png)
![Screenshot from 2022-04-26 19-26-50](https://user-images.githubusercontent.com/42603768/165290006-6506fb2b-d4c1-4f5b-b5a4-82e6797ab2b3.png)

Unlike the pytorch that use the *period of first restart* `T_0` and the *period multiplicative factor* `T_mult` to control the lr, the `mmaction2` requires to specify the periods of all restarts. And it supports restarting with weights. For the above example, it has three consine restarts in its (5+10+15=30) epochs, the first two restarts begin with `base_lr` and end with `0.1*base_lr`, while the last restart begins with `0.5*base_lr` and end with `0.05*base_lr`. The weights is related to last restart.


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

0. Let's note the `clip_len`, `frame_interval`, and `num_clips` as (clip_len x frame_interval x clip_len), e.g. (16x4x2)
1. **`SampleFrames(16x4x1)`:** This is the most frequently used, and the most basic sampling strategy. It randomly samples *16* frames with interval *4* from the input video. It can be regarded as first randomly cropping a 64-frame sub-video from the input video, then uniformally sampling 16 frames from the cropped sub-video. The output shaoe is (16, H, W)
2. `SampleFrames(16x4x8)`: Now the `num_clips=8`, this can be regarded as first dividing the input video into *8* sub-videos, then conducting the same operations as the `SampleFrames(16x4x1)` on each sub-video. This example is just for explanation. I have never seen such a combiantion of arguments. The output shape is (256, H, W)
3. **`SampleFrames(1x1x8)`:** Same as the point 2, but in this case we only randomly sample *one* frame in each of the *8* sub-videos. This sampling strategy is what the *TSN* proposes to use to cover global information of the videos. And I found that TSN is the only one using this kind of arguments combination. The output shape is (8, H, W), whereas accoring to the TSN, each of the 8 frames will be seperately fed into a 2D ResNet, i.e., the 8 will be multiplied into the batch_size dimension.
4. `DenseSampleFrames(16x4x2)`: Similar to `SampleFrames`, but in this case it first crop a sub-video of length 64-frames (arg of `DenseSampleFrames` default=64) from the input video, then apply the `SampleFrames(16x4x2)` on the cropped sub-video. When the `num_clips=1`, `DenseSampleFrames` is same as the `SampleFrames`. This pipeline is hardly used in mmaction2 and I have never seen similar sampling strategy in the literatures. Maybe it's a experimental pipeline.

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
Random crop that specifics the area and height-weight ratio range.
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

- This data augmentation mimic the `torch.RandomResizedCrop`, which widly used in image-based tasks, known as the Inception-style random cropping. It first determine the cropping shape, whose aspect ratio and area is randomly selected in the given range. Then with the specific cropping shape, it randomly crop a sub-area from the input.

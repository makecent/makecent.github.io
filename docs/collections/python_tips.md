---
layout: default
title: Python
parent: Collections
nav_order: 3
---

# Useful Package
## namedtuple
```python
from collections import namedtuple
BBox = namedtuple('bbox', ['start', 'end', 'label'])
box = BBox(2.0, 56.1, 'Cat')

# Example 1
start, end, label = box
start = box[0]

# Example 2
length = box.end - box.start

# Example 3
def get_annoatation():
    ...
    yield BBox(start_i, end_i, label_i)
    
start_x, end_x, label_x = get_annoatation() # or
box = get_annotation()

# Example 4
from typing import NamedTuple
class BBox(NamedTuple):
    start: int
    end: int
    label: str
```

## argparse
When running `.py` file in command line, `argparse` can be used to warp arguments. For example:
```console
foo@bar:~$ python test.py --batch-size 64 --data-root ./my_data/ImageNet
foo@bar:~$ python test.py --help    # (or -h) It will print the arg infomation.   
```
Below is an example `.py` file.
```python
import argparse

def parse_args():
    # you don't need to configure the argment --help, which will be automatically set by the package.
    parser = argparse.ArgumentParser(description='deliver some messages')
    parser.add_argument("positional arg1", help="get value of arg1 from the first arg in the command line, simple but not recommended")
    parser.add_argument("positional arg2", help="get value of arg2 from the second arg in the command line")
    parser.add_argument("--named_arg", help="an arg with name, get value from the `--an_named_arg` in the command line")
    parser.add_argument("--int_arg", help="an arg with type constrain", type=int)
    parser.add_argument("--arg_with_default", help="an arg with default value", default=5)
    parser.add_argument("--limited_arg", help="an arg limited in specific choices", choice=[1, 5, 6])
    parser.add_argument("--flag_arg1", help="flag_arg1=True if --flag_arg1 else False", action="store_ture")
    parser.add_argument("--flag_arg2", help="flag_arg2=False if --flag_arg2 else True", action="store_false")
    parser.add_argument("--list_arg", help="an arg of type list, can take multiple values", narg="+")
    return parser.parse_args()

args = parse_args()
named_arg = args.named_arg
```
Links:

[Python3 official argpase page](https://docs.python.org/3/library/argparse.html)

[A simple argpase tutorial](https://docs.python.org/3/howto/argparse.html)

# Wheels
## Hyper-parameter Search
Below is an real python script for searching best hyper-parameters, e.g., IoU_threshold in NMS.
```python
import heapq
import os.path as osp
from collections import namedtuple

import mmcv
import numpy as np
import torch
from matplotlib import pyplot as plt
from mmaction.datasets.builder import build_dataset
from mmcv import Config
from tqdm import tqdm

from custom_modules.mmdet_utils import bbox2result, eval_map
from custom_modules.mmdet_utils import multiclass_nms

cfg_file = Config.fromfile("configs/apn_r3dsony_32x4_10e_thumos14_flow.py")
det_before_nms = mmcv.load(osp.join(cfg_file.work_dir, 'det_before_nms.pkl'))
ds = build_dataset(cfg_file.data.test)
annotations = ds.get_ann_info()
classes = ds.CLASSES


def nms(det_bbox, cls_score, loc_score, score_thr, nms_thr, max_per_video):
    det_bbox, cls_score, loc_score = map(torch.from_numpy, (det_bbox, cls_score, loc_score))
    det_bbox, det_label = multiclass_nms(
        det_bbox,
        cls_score,
        score_thr,
        dict(iou_thr=nms_thr),
        max_per_video,
        score_factors=loc_score)
    return det_bbox, det_label


class Param:
    def __init__(self, name, grid):
        self.name = name
        self.grid = grid
        self.num = len(grid)
        self.grid_name = [f"{x:.2f}" if isinstance(x, float) else f"{x}" for x in grid]

    def __len__(self):
        return self.num

    def __getitem__(self, idx):
        return self.grid[idx]


class SearchSpace:

    def __init__(self, params):
        self.params = params
        self.space = [len(p) for p in params]
        self.total_num = len(self)
        self.num_params = len(params)
        self.param_names = [p.name for p in params]
        self.P = namedtuple('Params', [p.name for p in params])
        self.result = []

    def __len__(self):
        return int(np.prod(self.space))

    def __getitem__(self, idx):
        if idx >= len(self):
            raise IndexError
        inds = np.unravel_index(idx, self.space)
        return self.P(*[p[inds[i]] for i, p in enumerate(self.params)])

    def boxplot(self, param_specify, **kwargs):
        if isinstance(param_specify, int):
            param_ind = param_specify
            param_name = self.param_names[param_ind]
        elif isinstance(param_specify, str):
            param_ind = self.param_names.index(param_specify)
            param_name = param_specify
        else:
            raise TypeError
        result = np.array(self.result).reshape(self.space)
        data = [d.flatten() for d in np.split(result, self.space[param_ind], axis=param_ind)]
        plt.boxplot(data, **kwargs)
        plt.xticks(list(range(1, len(data) + 1)), self.params[param_ind].grid_name)
        plt.xlabel('Choice')
        plt.ylabel('Score')
        plt.title(param_name)
        plt.grid()
        plt.show()

    def topkplot(self, k=10):
        assert k <= self.total_num
        x = list(range(self.num_params))
        yy = []
        topk = heapq.nlargest(k, enumerate(self.result), key=lambda x: x[1])
        for n, (idx, value) in enumerate(topk):
            y = np.unravel_index(idx, self.space)
            yy.append(y)
            plt.plot(x, y, label=f'top{n + 1} {value:.2f}')
        plt.legend()
        plt.xticks(list(range(self.num_params + 2)), self.param_names + ['', ''])
        plt.yticks(list(range(np.max(yy) + 2)))
        plt.title(f"Top {k} settings")
        plt.ylabel('Choice')
        plt.grid()
        plt.show()


param1 = Param('nms_thr', np.arange(0, 1.0, 0.1))
param2 = Param('score_thr', np.arange(0, 1.0, 0.1))
param3 = Param('max_per_video', np.arange(0, 200, 40))
search_space = SearchSpace([param1, param2, param3])
for params in tqdm(search_space):
    det_results = []
    for results_by_video in det_before_nms:
        det_bbox, cls_score, loc_score = results_by_video
        det_bbox, det_label = nms(det_bbox, cls_score, loc_score,
                                  params.score_thr,
                                  params.nms_thr,
                                  params.max_per_video)
        det_results.append((det_bbox, det_label))
    det_results = [bbox2result(det_bboxes, det_labels, len(classes)) for det_bboxes, det_labels in det_results]
    iou_thr = 0.5
    mean_ap, _ = eval_map(det_results, annotations, iou_thr=iou_thr, dataset=classes)
    mean_ap = round(mean_ap, 3)
    # mean_ap = np.random.rand()
    search_space.result.append(mean_ap)

search_space.boxplot(0)
search_space.topkplot(5)
print('finished')

```
**Outputs:**

![1](https://user-images.githubusercontent.com/42603768/169227419-b68c6e8a-0b37-4d35-8a0d-1a2f720ceead.png)

![2](https://user-images.githubusercontent.com/42603768/169227431-01aa2795-6951-4971-840d-de8bb1217c8a.png)


# Miscellaneous
## Iteration, Iterable and Iterator

A greate blog explaining this (in Chinese): [Python進階技巧 (6) — 迭代那件小事：深入了解 Iteration / Iterable / Iterator / __iter__ / __getitem__ / __next__ / yield](https://medium.com/citycoddee/python%E9%80%B2%E9%9A%8E%E6%8A%80%E5%B7%A7-6-%E8%BF%AD%E4%BB%A3%E9%82%A3%E4%BB%B6%E5%B0%8F%E4%BA%8B-%E6%B7%B1%E5%85%A5%E4%BA%86%E8%A7%A3-iteration-iterable-iterator-iter-getitem-next-fac5b4542cf4)

## Conda upgrade failed
```terminal
conda update -n base -c defaults conda --repodata-fn=repodata.json
```

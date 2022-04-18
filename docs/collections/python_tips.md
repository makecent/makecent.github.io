---
layout: default
title: Python Practices
parent: Collections
nav_order: 3
---

# Collections
## namedtuple
```python
from collections import namedtuple
BBox = namedtuple('bbox', [start, end, label])
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

# Iteration, Iterable and Iterator

A greate blog explaining this (in Chinese): [Python進階技巧 (6) — 迭代那件小事：深入了解 Iteration / Iterable / Iterator / __iter__ / __getitem__ / __next__ / yield](https://medium.com/citycoddee/python%E9%80%B2%E9%9A%8E%E6%8A%80%E5%B7%A7-6-%E8%BF%AD%E4%BB%A3%E9%82%A3%E4%BB%B6%E5%B0%8F%E4%BA%8B-%E6%B7%B1%E5%85%A5%E4%BA%86%E8%A7%A3-iteration-iterable-iterator-iter-getitem-next-fac5b4542cf4)

# Something else

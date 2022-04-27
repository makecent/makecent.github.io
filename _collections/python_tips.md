---
layout: default
title: Python
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

# Package `argparse`
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

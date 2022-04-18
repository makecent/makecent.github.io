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

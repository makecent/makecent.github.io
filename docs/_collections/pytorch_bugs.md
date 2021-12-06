---
titlle: About python
---

### RuntimeError: CUDA error: device-side assert triggered
Normally because index out of tensor dimension, basically because wrong label values.
add `CUDA_LAUNCH_BLOCKING=1 python ...` to get better tracing about the position where bug happens. Somtimes together with move the tensor to cpu can give you more details

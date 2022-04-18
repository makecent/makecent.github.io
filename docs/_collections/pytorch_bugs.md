---
title: "Pytorch Bugs"
---

### RuntimeError: CUDA error: device-side assert triggered
Normally because index out of tensor dimension, frenquently happens to wrong label values.
Add `CUDA_LAUNCH_BLOCKING=1 python ...` to get better tracing about the position where bug happens. Somtimes together with move the tensor to cpu can give you more details

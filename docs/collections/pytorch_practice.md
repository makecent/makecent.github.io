---
layout: default
title: Pytorch Practices
parent: Collections
nav_order: 1
---

# Useful `Tensor` functions
```python
# batch-wise matrice multiplication
torch.bmm(A, B), A.bmn(B) 

# buffer frequently reused values (non-parameters) for speed up, e.g., positional embeddings
nn.Module.register_buffer(name, tensor, persistent=True) 

# Returns the upper/lower triangular part of the matrix, the other elements are set to 0.
torch.triu(A), A.triu()
torch.tril(A), A.tril()

# Directly return the max values along the specific dimension. No need of using `max_v, _ = A.max(dim=1)`
A.max(dim=1).values, A.max(dim=1).indices 

# Get the topk (top-5 here) values and indices. `.values` and `.indices` can be used to access the specific part.
A.topk(5)

# Matrix multiplication on the last two dimensions of tensor A and B. Multiplication (with Broadcasting) on the remaining dimensions.
A @ B 

# Casting tensor to target dtype
A.double(), A.float(), A.long(), A.int(), A.bool()

# Convert A to the type of B, including `dtype` and `device`.
A.type_as(B) 

# Get zero/one tensor like a specific tensor
A.new_zeros(size, dtype=None, device=None, requires_grad=False), A.new_ones(...)
torch.zeros_like(A), torch.ones_like(A)   # including size

# Apply function
A.apply_(func)
A.map_(B, func)   # B as argument

# Element-wise logical operators (and, or, xor)
A & B, A | B, A ^ B

# Reshape specific dimension(s) with `flatten` and `unflatten`. Alternatives: `nn.Unflatten` and `nn.Flatten`
A.unflatten(dim, size)  # reshape the specific dimension to size
A.flatten(start_dim, end_dim) # reshape the specific dimensions to 1.

# Move dimension
A.move(source, destination)
```
# Useful `Module` function
## [`Module.register_forward_hook(hook)`](https://pytorch.org/docs/stable/generated/torch.nn.Module.html?highlight=register_forward_hook#torch.nn.Module.register_forward_hook)
Retrieve the weigths in a model is easy, but what about retrieve an output, e.g. activation after layer1.ReLU, in model with a given input? We can use the `Module.register_module_forward_hook(hook)` to do this. For example
```python
features = []
def hook(module, input, output): 
    features.append(output.clone().detach())

net = LeNet() 
x = torch.randn(2, 3, 32, 32)  
handle = net.conv2.register_forward_hook(hook)
y = net(x)
print(features[0])
handle.remove()
```

# Params and FLOPs
> Flop is not a well-defined concept.

Results comparison:

| X3D-M | fvcore | torchinfo | thop | official |
|:-------|:------|:------|:------|:-----|
| params (M) | 3.79 | 3.79 | 3.79 | 3.8 |
| FLOPs (G)  | 5.14 | 4.73 | 4.90 | 6.2 |

| ResNet50 | fvcore | torchinfo | thop | official |
|:-------|:------|:------|:------|:-----|
| params (M) | 25.56 | 25.56 | 25.56 | 25.6 |
| FLOPs (G)  | 4.14 | 4.09 | 4.11 | 3.8 |

## [fvcore](https://github.com/facebookresearch/fvcore)
> FAIR is responsible for maintaining this library.

Installation:
```shell
pip install -U fvcore
```

Example 1:
```python
import torch
from fvcore.nn import FlopCountAnalysis, parameter_count

imgs = torch.randn(1, 3, 16, 224, 224)
model = torch.hub.load("facebookresearch/pytorchvideo", model="x3d_m", pretrained=False)

flops = FlopCountAnalysis(model, imgs)
print(flops.total())

params = parameter_count(model)
print(params[''])
```

FLOPs Output:
```shell
Using cache found in /home/louis/.cache/torch/hub/facebookresearch_pytorchvideo_main
Unsupported operator aten::add_ encountered 83 time(s)
Unsupported operator aten::mean encountered 15 time(s)
Unsupported operator aten::sigmoid encountered 15 time(s)
Unsupported operator aten::mul encountered 15 time(s)
Unsupported operator prim::PythonOp.SwishFunction encountered 26 time(s)
Unsupported operator aten::add encountered 26 time(s)
Unsupported operator aten::avg_pool3d encountered 1 time(s)
Unsupported operator aten::softmax encountered 1 time(s)
Unsupported operator aten::adaptive_avg_pool3d encountered 1 time(s)
5141904896
3794274
```

Example 2 (mmlab-style):
```python
import torch
from fvcore.nn import FlopCountAnalysis, parameter_count, flop_count_table
from mmcv import Config
from mmaction.models.builder import build_model, build_backbone

inputs = torch.randn(1, 3, 16, 224, 224)
model = build_backbone(Config.fromfile("configs/mvit/mvit_16x4_kinetics400_video.py").model.backbone)

# inputs = (torch.randn(1, 1, 3, 16, 224, 224), torch.randint(10, (1, 1)))
# model = build_model(Config.fromfile("configs/mvit/mvit_16x4_kinetics400_video.py").model)

flops = FlopCountAnalysis(model, inputs)
print(flop_count_table(flops))
params = parameter_count(model)

print(f"FLOPS:\t{flops.total()}")
print(f"Params:\t{params['']}")
```

More details can be found in this [docs](https://github.com/facebookresearch/fvcore/blob/main/docs/flop_count.md), API of [FlopCountAnalysis](https://detectron2.readthedocs.io/en/latest/modules/fvcore.html#fvcore.nn.FlopCountAnalysis), API of [parameter_count](https://detectron2.readthedocs.io/en/latest/modules/fvcore.html#fvcore.nn.parameter_count)

## [torchinfo](https://github.com/TylerYep/torchinfo)
> mimc the tensorflow summary 

Installation:
```shell
pip install torchinfo
```

Example:
```python
import torch
from torchinfo import summary
model = torch.hub.load("facebookresearch/pytorchvideo", model="x3d_m", pretrained=False)
summary(model, input_size=(1, 3, 16, 224, 224))
```

Output:
```shell
==============================================================================================================
Layer (type:depth-idx)                                       Output Shape              Param #
==============================================================================================================
Net                                                          --                        --
??????ModuleList: 1-1                                            --                        --
???    ??????ResStage: 2                                           --                        --
???    ???    ??????ModuleList: 3-1                                  --                        15,370
???    ??????ResStage: 2                                           --                        --
???    ???    ??????ModuleList: 3-2                                  --                        73,248
???    ??????ResStage: 2                                           --                        --
???    ???    ??????ModuleList: 3-3                                  --                        569,256
???    ??????ResStage: 2                                           --                        --
???    ???    ??????ModuleList: 3-4                                  --                        1,347,440
???    ??????ResNetBasicStem: 2-1                                  [1, 24, 16, 112, 112]     --
???    ???    ??????Conv2plus1d: 3-5                                 [1, 24, 16, 112, 112]     768
???    ???    ??????BatchNorm3d: 3-6                                 [1, 24, 16, 112, 112]     48
???    ???    ??????ReLU: 3-7                                        [1, 24, 16, 112, 112]     --
???    ??????ResStage: 2-2                                         [1, 24, 16, 56, 56]       --
???    ??????ResStage: 2-3                                         [1, 48, 16, 28, 28]       --
???    ??????ResStage: 2-4                                         [1, 96, 16, 14, 14]       --
???    ??????ResStage: 2-5                                         [1, 192, 16, 7, 7]        --
???    ??????ResNetBasicHead: 2-6                                  [1, 400]                  --
???    ???    ??????ProjectedPool: 3-8                               [1, 2048, 1, 1, 1]        968,544
???    ???    ??????Dropout: 3-9                                     [1, 2048, 1, 1, 1]        --
???    ???    ??????Linear: 3-10                                     [1, 1, 1, 1, 400]         819,600
???    ???    ??????Softmax: 3-11                                    [1, 400, 1, 1, 1]         --
???    ???    ??????AdaptiveAvgPool3d: 3-12                          [1, 400, 1, 1, 1]         --
==============================================================================================================
Total params: 3,794,274
Trainable params: 3,794,274
Non-trainable params: 0
Total mult-adds (G): 4.73
==============================================================================================================
Input size (MB): 9.63
Forward/backward pass size (MB): 1358.41
Params size (MB): 15.18
Estimated Total Size (MB): 1383.22
==============================================================================================================
```

## [thop](https://github.com/Lyken17/pytorch-OpCounter)
> Simple, 3.4k stars

Installation:
```shell
pip install thop
```

Example:
```python
import torch
from thop import profile
imgs = torch.randn(1, 3, 16, 224, 224)
model = torch.hub.load("facebookresearch/pytorchvideo", model="x3d_m", pretrained=False)
macs, params = profile(model, inputs=(imgs, ))
print(macs, params)
```

Output:
```shell
[INFO] Register count_convNd() for <class 'torch.nn.modules.conv.Conv3d'>.
[WARN] Cannot find rule for <class 'pytorchvideo.layers.convolutions.Conv2plus1d'>. Treat it as zero Macs and zero Params.
[INFO] Register count_bn() for <class 'torch.nn.modules.batchnorm.BatchNorm3d'>.
[INFO] Register zero_ops() for <class 'torch.nn.modules.activation.ReLU'>.
[WARN] Cannot find rule for <class 'pytorchvideo.models.stem.ResNetBasicStem'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'torch.nn.modules.activation.Sigmoid'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'torch.nn.modules.container.Sequential'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'fvcore.nn.squeeze_excitation.SqueezeExcitation'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.layers.swish.Swish'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.models.resnet.BottleneckBlock'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.models.resnet.ResBlock'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'torch.nn.modules.linear.Identity'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'torch.nn.modules.container.ModuleList'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.models.resnet.ResStage'>. Treat it as zero Macs and zero Params.
[INFO] Register count_avgpool() for <class 'torch.nn.modules.pooling.AvgPool3d'>.
[WARN] Cannot find rule for <class 'pytorchvideo.models.x3d.ProjectedPool'>. Treat it as zero Macs and zero Params.
[INFO] Register zero_ops() for <class 'torch.nn.modules.dropout.Dropout'>.
[INFO] Register count_linear() for <class 'torch.nn.modules.linear.Linear'>.
[WARN] Cannot find rule for <class 'torch.nn.modules.activation.Softmax'>. Treat it as zero Macs and zero Params.
[INFO] Register count_adap_avgpool() for <class 'torch.nn.modules.pooling.AdaptiveAvgPool3d'>.
[WARN] Cannot find rule for <class 'pytorchvideo.models.head.ResNetBasicHead'>. Treat it as zero Macs and zero Params.
[WARN] Cannot find rule for <class 'pytorchvideo.models.net.Net'>. Treat it as zero Macs and zero Params.
4896248152.0 3794274.0
```

## Benchmarking
```python
# %% fvcore
import torch
from fvcore.nn import FlopCountAnalysis, parameter_count, flop_count_table
from mmcv import Config
from mmaction.models.builder import build_model, build_backbone
# torch.backends.cudnn.benchmark=True
inputs = torch.randn(1, 1, 3, 32, 224, 224).cuda()
# model = build_backbone(Config.fromfile(
#     "configs/apn_r3dsony_8x32_10e_aty13_video.py").model.backbone).cuda()

model = build_model(Config.fromfile(
    "configs/apn_r3dsony_8x32_10e_aty13_video.pyy").model).cuda()

# flops = FlopCountAnalysis(model.backbone, inputs)
# print(flop_count_table(flops, max_depth=10))
# params = parameter_count(model.backbone)
#
# print(f"GFLOPS:\t{flops.total()/1e9:.2f} G")
# print(f"Params:\t{params['']/1e6:.2f} M")

from mmcv import track_iter_progress
for i in track_iter_progress(list(range(10000))):
    # loss = model(inputs, class_label=torch.randint(20, (1, 1)).cuda(), return_loss=True)
    # loss['loss_cls'].backward()

    with torch.no_grad():
        out = model(inputs, return_loss=False)
```

# Miscellaneous

## Be careful about `F.dropout`
Unlike `nn.Dropout` which will be automatically shutdown after `model.eval()`, `F.dropout` requires an argument `training=True/False` to determine whether it's on or off. In short, using `nn.Dropout` as possible.

---
title:  "Installation and Implementation Example of SlowFast and PytorchVideo (FAIR)"
---

# PytorchVideo Installation (15 April 2022)
```bash
conda create -n pytorchvideo
conda activate pytorchvideo
conda install pytorch torchvision -c pytorch 
conda install -c conda-forge -c fvcore -c iopath fvcore=0.1.4 iopath
pip install pytorchvideo
```

## Example
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin https://dl.fbaipublicfiles.com/pyslowfast/dataset/class_names/kinetics_classnames.json https://dl.fbaipublicfiles.com/pytorchvideo/projects/archery.mp4
```
```python
import json

import torch
from pytorchvideo.data.encoded_video import EncodedVideo
from pytorchvideo.transforms import (
    ApplyTransformToKey,
    ShortSideScale,
    UniformTemporalSubsample
)
from torchvision.transforms import Compose, Lambda
from torchvision.transforms._transforms_video import (
    CenterCropVideo,
    NormalizeVideo,
)

# Device on which to run the model
# Set to cuda to load on GPU
device = "cpu"

# Pick a pretrained model and load the pretrained weights
# Use torch.hub.list("facebookresearch/pytorchvideo:main") to check all available models. Take care about the data pre-processing required by models.
model_name = "mvit_base_16x4"
model = torch.hub.load("facebookresearch/pytorchvideo", model=model_name, pretrained=True)

# Set to eval mode and move to desired device
model = model.to(device)
model = model.eval()

with open("kinetics_classnames.json", "r") as f:
    kinetics_classnames = json.load(f)

# Create an id to label name mapping
kinetics_id_to_classname = {}
for k, v in kinetics_classnames.items():
    kinetics_id_to_classname[v] = str(k).replace('"', "")

####################
# SlowFast transform
####################

side_size = 256
mean = [0.45, 0.45, 0.45]
std = [0.225, 0.225, 0.225]
crop_size = 224
num_frames = 16
sampling_rate = 4
frames_per_second = 30
alpha = 4


transform = ApplyTransformToKey(
    key="video",
    transform=Compose(
        [
            UniformTemporalSubsample(num_frames),
            Lambda(lambda x: x / 255.0),
            NormalizeVideo(mean, std),
            ShortSideScale(
                size=side_size
            ),
            CenterCropVideo(crop_size),
        ]
    ),
)

# The duration of the input clip is also specific to the model.
clip_duration = (num_frames * sampling_rate) / frames_per_second

video_path = "archery.mp4"

# Select the duration of the clip to load by specifying the start and end duration
# The start_sec should correspond to where the action occurs in the video
start_sec = 0
end_sec = start_sec + clip_duration

# Initialize an EncodedVideo helper class
video = EncodedVideo.from_path(video_path)
    
# Load the desired clip
video_data = video.get_clip(start_sec=start_sec, end_sec=end_sec)

# Apply a transform to normalize the video input
video_data = transform(video_data)

# Move the inputs to the desired device
inputs = video_data["video"]
inputs = inputs.to(device)

preds = model(inputs[None, ...])
# Get the predicted classes
post_act = torch.nn.Softmax(dim=1)
preds = post_act(preds)
pred_classes = preds.topk(k=5).indices

# Map the predicted classes to the label names
pred_class_names = [kinetics_id_to_classname[int(i)] for i in pred_classes[0]]
print("Predicted labels: %s" % ", ".join(pred_class_names))

```

# SlowFast Installation (15 April 2022)
```bash
git clone https://github.com/facebookresearch/SlowFast.git
cd SlowFast
git clone https://github.com/facebookresearch/detectron2.git

conda create -n slowfast python=3.8
conda activate slowfast
conda install pytorch torchvision -c pytorch
python -m pip install -e detectron2
nano setup.py     #手动将PIL改称pillow
pip install -v -e .
rm -rf detectron2
```

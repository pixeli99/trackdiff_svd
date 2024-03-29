<div align="center">
<img src="assets/figs/favicon.png" alt="TrackDiffusion" width="30%">
</div>

## TrackDiffusion: Tracklet-Conditioned Video Generation via Diffusion Models

Pytorch implementation of [TrackDiffusion: Tracklet-Conditioned Video Generation via Diffusion Models](https://arxiv.org/abs/2312.00651)

## Abstract 

Despite remarkable achievements in video synthesis, achieving granular control over complex dynamics, such as nuanced movement among multiple interacting objects, still presents a significant hurdle for dynamic world modeling, compounded by the necessity to manage appearance and disappearance, drastic scale changes, and ensure consistency for instances across frames.
These challenges hinder the development of video generation that can faithfully mimic real-world complexity, limiting utility for applications requiring high-level realism and controllability, including advanced scene simulation and training of perception systems.
To address that, we propose TrackDiffusion, a novel video generation framework affording fine-grained trajectory-conditioned motion control via diffusion models, which facilitates the precise manipulation of the object trajectories and interactions, overcoming the prevalent limitation of scale and continuity disruptions. 
A pivotal component of TrackDiffusion is the instance enhancer, which explicitly ensures inter-frame consistency of multiple objects, a critical factor overlooked in the current literature.
Moreover, we demonstrate that generated video sequences by our TrackDiffusion can be used as training data for visual perception models.
To the best of our knowledge, this is the first work to apply video diffusion models with tracklet conditions and demonstrate that generated frames can be beneficial for improving the performance of object trackers.

## Method

The framework generates video frames based on the provided tracklets and employs the **Instance Enhancer** to reinforce the temporal consistency of foreground instance. A new gated cross-attention layer is inserted to take in the new instance information..

![framework](assets/figs/framework.png)

## Getting Started

### Environment Setup
The code is tested with Pytorch==2.0.1 and cuda 11.8 on A800 servers. To setup the python environment, follow:
```bash
cd ${ROOT}
pip install -r requirements.txt
# continue to install `third_party`s
```
### Pretrained Weights
Our training are based on `stabilityai/stable-video-diffusion-img2vid`. You can access the following links to obtain the weights for stage1 and stage2:

[Stage1]()

[Stage2]()
## Training

### 1. Convert Annotations

We use CocoVID to maintain all datasets in this codebase. In this case, you need to convert the official annotations to this style. We provide scripts and the usages are as following:

```python
cd ./third_party/mmtracking

# YouTube-VIS 2019
python ./tools/dataset_converters/youtubevis/youtubevis2coco.py -i ./data/youtube_vis_2019 -o ./data/youtube_vis_2019/annotations --version 2019

# YouTube-VIS 2021
python ./tools/dataset_converters/youtubevis/youtubevis2coco.py -i ./data/youtube_vis_2021 -o ./data/youtube_vis_2021/annotations --version 2021
```
The folder structure will be as following after your run these scripts:
```
│   ├── youtube_vis_2019
│   │   │── train
│   │   │   │── JPEGImages
│   │   │   │── ......
│   │   │── valid
│   │   │   │── JPEGImages
│   │   │   │── ......
│   │   │── test
│   │   │   │── JPEGImages
│   │   │   │── ......
│   │   │── train.json (the official annotation files)
│   │   │── valid.json (the official annotation files)
│   │   │── test.json (the official annotation files)
│   │   │── annotations (the converted annotation file)
│   │
│   ├── youtube_vis_2021
│   │   │── train
│   │   │   │── JPEGImages
│   │   │   │── instances.json (the official annotation files)
│   │   │   │── ......
│   │   │── valid
│   │   │   │── JPEGImages
│   │   │   │── instances.json (the official annotation files)
│   │   │   │── ......
│   │   │── test
│   │   │   │── JPEGImages
│   │   │   │── instances.json (the official annotation files)
│   │   │   │── ......
│   │   │── annotations (the converted annotation file)
```
### 2. Two-Stage Training

**Stage 1: Training with RGB boxes**

Launch training with (with 8xA800):
```bash
bash ./scripts/stage1.sh
```

**Stage 2: Training with boxes only**

Launch training with (with 8xA800):
```bash
bash ./scripts/stage2.sh
```

### 3. WebDemo
To simplify the interaction, we use an LLM to automatically generate trajectories, so please remember to replace the OpenAI key in the file(`client.api_key`).
```bash
python web_demo.py
```

- Upload an image.
- Click the **Detect Objects** button
- Write your motion prompt (based on GPT-4)
- Click **Gen Video** button

![webdemo](assets/figs/webdemo.png)
## Results

- Compare TrackDiffusion with other methods for generation quality:

![main_results](./assets/figs/demo.png)

- Training support with frames generated from TrackDiffusion:

<div align="center">
<img src="assets/figs/trainability.png" alt="train" width="70%">
</div>

More results can be found in the main paper.



---
layout: page
title: Real-Time "Enemy" Robot Detection
description: ""
img: assets/img/3.jpg
importance: 2
category: work
giscus_comments: true
---

### Background
I am a Software Lead focusing on CV/ML for [CU Robotics](https://curobotics.net/). We compete in the [Arc Robotics](https://www.arc-robotics.org/) competition, where a primary way to score points is by detecting and shooting our opponents' armor plates. 

As such, I co-led the creation of our entire machine learning pipeline for armor plate detection, from data collection to inference.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/purdue_comp.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true %}
    </div>
</div>
<div class="caption">
    Demo of the current models in action, showing plate detection (color & confidence on bottom) and icon detection (icon & confidence on top).
</div>

### Dataset Collection

There was no existing dataset for this task, so we collected our own.

Our plate detection dataset was created by taking photos of our robots with plates attached in different scenarios and floating plates to not overfit to our robots.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/CU_Robotics/plate_dataset.jpg" title="Plate Dataset" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Plate Dataset example.
</div>


We used LabelStudio as our labeling software, and SAM3 as our autolabeler to essentially distill knowledge into our smaller model.

### Model Selection and Training

Our hardware is a Jetson Orin AGX and 160fps cameras. Since this is a very fast paced competition, we prioritized inference speed for our pipeline. 

- **Plate Detection**: We went with Ultralytics' yolo26 nano model, seeing that TensorRT optimizations on a T4 chip brought inference down to around 3ms on their website.
- **Icon Classification**: We ended up making a custom CNN with 7 layers. We evaluated Ultralytics' yolo26 nano classifier as well but found that it was much larger than what was needed for simple classifying.

We trained the models on Google Colab, using Comet as a visualizer to check training and finetune parameters as needed.

### Implementation

After training the models, we exported them as `.engine` files through TensorRT with half precision for more speed. Additionally since we will often see more than 1 plate in an image, we made sure to batch our icon classifier to be able to classify multiple plates at once. 



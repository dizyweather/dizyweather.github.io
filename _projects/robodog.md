---
layout: page
title: Unitree Go1 Data Collection and Localization
description: 2026
img: assets/img/go1/robodog_walk.gif
importance: 2
category: work
related_publications: false
---

Working under Dr. [Shivendra Agrawal](https://shivendraagrawal.github.io/), I helped collect data using the Unitree Go1 quadruped robot in various environments and streamlined the workflow for future data collection.

### Data Collection
The Unitree Go1 only comes with onboard cameras, which are low resolution and heavily fisheye. For our purposes, we integrated external sensors onto the robot to capture higher-quality data. The sensor payload includes:

<div class="row mt-3">
<div class="col-sm-6 mt-3 mt-md-0" markdown="1">

- **Velodyne VLP-16 3D LiDAR**: 
   - For localization and initial map generation
- **RealSense D455 Camera**:
   - To capture clear RGB images and depth data for vision-language model (VLM) annotations down the line
- **Generic Webcam**:
   - Facing upward to detect ArUco codes on the ceiling for localization in environments with ArUco markers
  - _Note: While not labeled in the diagram, you can see it is mounted behind the Velodyne LiDAR, slightly outlined in grey._

</div>
<div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/go1/go1_diagram.png" title="Unitree Go1 Sensor Setup" class="img-fluid rounded z-depth-1" %}
</div>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/go1/lidar_reconstruction.png" title="LiDAR Reconstruction" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    3D LiDAR reconstruction of the hallway surrounding the lab.
</div>

### Ceiling ArUco Codes


<div class="row mt-3">
<div class="col-sm-6 mt-3 mt-md-0" markdown="1">

Helped mount ArUco codes to the ceiling of the lab for future localization experiments and for testing robot state estimation. We positioned them closely to ensure the upward-facing webcam can detect at least two markers from anywhere in the lab, providing redundancy.

To localize the ArUco codes themselves, we used the Velodyne LiDAR to generate a pointcloud map of the lab and utilized an existing package to locate the relative positions of the markers within that map.

</div>
<div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/go1/aruco_code_ceiling_crop.jpg" title="Unitree Go1 Sensor Setup" class="img-fluid rounded z-depth-1" %}
</div>
</div>



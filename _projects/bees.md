---
layout: page
title: Honeybee Swarm Formation
description: Experimental setup and analysis of honeybee swarms
img: assets/img/bees/swarm_cropped.jpg
importance: 3
category: work
---

[Link to Paper: https://doi.org/10.64898/2026.03.17.711698](https://doi.org/10.64898/2026.03.17.711698)

## Intro & Why It's Important

Whether it’s a flock of geese flying in a V formation, a school of fish dodging a predator in sync, or termites building their complex nests, these feats of complex action are not orchestrated by any single organism. Instead, they emerge from the decentralized interactions of many individuals following simple rules. 

Understanding these self-organizing systems is incredibly valuable. Not only does it provide profound insight into biological evolution and animal behavior, but it also directly inspires advancements in collective robotics, swarm intelligence, and distributed computing. By studying how honeybees coordinate to form swarms, we can extract principles to develop better decentralized algorithms for drone fleets, improve robust communication protocols, and design artificial systems that remain highly resilient even when individual components fail.

## My Work

I designed, built, and implemented an experimental setup with the team that successfully captured honeybee swarm formation in the natural environment. My contributions focused on the hardware integration, automation, and the computer vision tools required to reliably collect and analyze the swarm data.

- **Motor Tracking & Defect Detection**: Created a program to track the rotation of motors, detecting deviations in rotation speed over long periods of time to identify mechanical defects and ensure our experimental apparatus ran smoothly.
- **Camera Localization & Synchronization**: Developed an automation pipeline for camera calibration and synchronization between two stereo cameras, which is critical for accurate 3D tracking of the bees.
- **Custom Hardware Design**: Designed and 3D printed custom GoPro mounts and adapters to securely attach the recording equipment to our experimental setup under varying outdoor conditions.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        <!-- Placeholder for 3D model image -->
        {% include figure.liquid path="assets/img/placeholder.png" title="3D Model Placeholder" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Placeholder for 3D model of custom mounts.
</div>

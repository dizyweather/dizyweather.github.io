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

<!-- Load the <model-viewer> web component for interactive 3D models -->
<script type="module" src="https://ajax.googleapis.com/ajax/libs/model-viewer/3.5.0/model-viewer.min.js"></script>

<div class="row mt-3 justify-content-center">
    <div class="col-md-8 mt-3 mt-md-0">
        <!-- 
          Interactive 3D model viewer.
          To use your own 3D model:
          1. Export your 3D design as a .glb or .gltf file (GLB is recommended for best performance).
          2. Save the file to assets/img/bees/mount_model.glb
          3. Change the 'src' attribute below to "{{ '/assets/img/bees/mount_model.glb' | relative_url }}"
        -->
        <model-viewer 
            src="{{ '/assets/img/bees/mount_model.glb' | relative_url }}" 
            alt="Interactive 3D model of custom GoPro mounts" 
            auto-rotate 
            camera-controls 
            ar 
            shadow-intensity="1" 
            style="width: 100%; height: 400px; background-color: #f8f9fa; border-radius: 8px; border: 1px solid #ddd;"
            class="z-depth-1"
        >
        </model-viewer>
    </div>
</div>
<div class="caption">
    Interactive 3D model of the custom GoPro mounts (drag to rotate, scroll to zoom!).
</div>

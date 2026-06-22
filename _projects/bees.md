---
layout: page
title: Experiment Design and Data Collection of Honeybee Swarms
description: 2025
img: assets/img/bees/swarm_cropped.jpg
importance: 3
category: work
---

<div class="publications">
{% bibliography -f papers -q @*[topic=swarm]* %}
</div>

## Background

I participated in the Summer 2025 SPUR program as part of the [Peleg Lab](https://www.peleglab.com/) under Danielle Chase, working on experiment design and data collection of honey bee swarm behavior in the wild.



Below are the presentation slides summarizing my research and findings. You can also [check out presentation slides with working videos](https://o365coloradoedu-my.sharepoint.com/:p:/g/personal/dazh5631_colorado_edu/IQDUnNsBIekLSKfyr2qy_NdGARjg3RkeuY2971pNmkt0EAA).

<div class="row justify-content-center mt-3">
  <div class="col-md-10">
    <object data="{{ '/assets/pdf/Daniel_Zhu_SPUR_2025_Presentation.pdf' | relative_url }}" type="application/pdf" width="100%" height="600px">
      <iframe src="{{ '/assets/pdf/Daniel_Zhu_SPUR_2025_Presentation.pdf' | relative_url }}#toolbar=0" width="100%" height="600px" style="border: 1px solid #ddd; border-radius: 8px;">
        <p>This browser does not support embedding PDFs. Please download the PDF to view it: <a href="{{ '/assets/pdf/Daniel_Zhu_SPUR_2025_Presentation.pdf' | relative_url }}">Download PDF</a>.</p>
      </iframe>
    </object>
  </div>
</div>

## My Work

I designed, built, and implemented an experimental setup with the team that successfully captured honeybee swarm formation in the natural environment. My contributions focused on the hardware integration, automation, and the computer vision tools required to reliably collect and analyze the swarm data.

- **[Camera Localization & Synchronization](https://github.com/dizyweather/Camera-Localization)**: Developed an automation pipeline for camera calibration and synchronization between two stereo cameras, which is critical for accurate 3D tracking of the bees.
- **Custom Hardware Design**: Designed and 3D printed custom GoPro mounts and adapters to securely attach the recording equipment to our experimental setup under varying outdoor conditions.

<!-- Load the <model-viewer> web component for interactive 3D models -->
<script type="module" src="https://ajax.googleapis.com/ajax/libs/model-viewer/3.5.0/model-viewer.min.js"></script>

<figure class="mt-3">
  <div class="row justify-content-center">
    <div class="col-md-8">
      <model-viewer
        id="gopro-mount-viewer"
        src="{{ '/assets/img/bees/mount_model.glb' | relative_url }}"
        alt="Interactive 3D model of custom GoPro mounts"
        auto-rotate
        camera-controls
        ar
        shadow-intensity="1"
        style="width: 100%; height: 400px; background-color: transparent; border-radius: 8px; border: 1px solid #ddd;"
        class="z-depth-1">
      </model-viewer>
    </div>
  </div>
  <!-- Dynamic 3D model coloring script -->
  <script>
    const viewer = document.getElementById("gopro-mount-viewer");
    viewer.addEventListener("load", () => {
      viewer.model.materials.forEach(material => {
        material.pbrMetallicRoughness.setBaseColorFactor([0.1, 0.5, 0.9, 1.0]);
      });
    });
  </script>
  <figcaption style="text-align: center;"><small>Interactive 3D model of the custom GoPro mounts (drag to rotate, scroll to zoom!).</small></figcaption>
</figure>

#### Design Decisions

- I designed for the use of same tightening screw as in commercial GoPro mounts
- Added hole for nut so you can screw and unscrew without needing to hold/align nut
- Middle prong on top is flat to stay flush with the bottom of the GoPro, making it face exactly straight ahead.
- Added aligning shaft to the bottom to fit into 8020 aluminum slider which helps make sure camera is parallel and inline with the other camera.
- Holes on both ends for 8020 channel nuts and screws

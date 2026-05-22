---
layout: page
title: Honeybee Swarm Formation
description: Experimental setup and analysis of honeybee swarms
img: assets/img/bees/swarm_cropped.jpg
importance: 3
category: work
---

{% reference chase20263d %}

## Background

I participated in the Summer 2025 SPUR program as part of the Peleg Lab under Danielle Chase, working on experiment design and data collection of bee swarm behavior.

## My Work

I designed, built, and implemented an experimental setup with the team that successfully captured honeybee swarm formation in the natural environment. My contributions focused on the hardware integration, automation, and the computer vision tools required to reliably collect and analyze the swarm data.


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
            id="gopro-mount-viewer"
            src="{{ '/assets/img/bees/mount_model.glb' | relative_url }}" 
            alt="Interactive 3D model of custom GoPro mounts" 
            auto-rotate 
            camera-controls 
            ar 
            shadow-intensity="1" 
            style="width: 100%; height: 400px; background-color: transparent; border-radius: 8px; border: 1px solid #ddd;"
            class="z-depth-1"
        >
        </model-viewer>

        <!-- Dynamic 3D model coloring script -->
        <script>
          const viewer = document.getElementById("gopro-mount-viewer");
          viewer.addEventListener("load", () => {
            // Dynamically paint all materials to a high-contrast professional blue
            viewer.model.materials.forEach(material => {
              // Color parameters: [Red, Green, Blue, Alpha] (all values between 0.0 and 1.0)
              material.pbrMetallicRoughness.setBaseColorFactor([0.1, 0.5, 0.9, 1.0]);
            });
          });
        </script>
    </div>
    </div>
    <details class="design-decision"><summary>Design Decisions</summary>
    <ul>
      <li>I designed for the use of same tightening screw as in commercial GoPro mounts</li>
      <li>Hole for nut so you can screw and unscrew without needing to hold/align nut</li>
      <li>Added aligning shaft to the bottom to help make sure camera is parallel to the 8020 aluminum and inline with the other camera</li>
      <li>Flat middle prong on top to make GoPros face exactly straight ahead (as we could adjust the height and position of the rest of the setup independently)</li>
    </ul>
    </details>
    <div class="caption">
    Interactive 3D model of the custom GoPro mounts (drag to rotate, scroll to zoom!).
</div>

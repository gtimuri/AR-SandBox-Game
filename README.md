# AR Sandbox Game: Real-Time Terrain Projection 🏎️

![Unity](https://img.shields.io/badge/Unity-2022.3-black?style=flat-square&logo=unity)
![C#](https://img.shields.io/badge/C%23-10.0-blue?style=flat-square&logo=csharp)
![Azure Kinect](https://img.shields.io/badge/Hardware-Azure_Kinect-0078D4?style=flat-square)
![OpenCV](https://img.shields.io/badge/Lib-OpenCV-red?style=flat-square&logo=opencv)

<img width="601" height="303" alt="Main menu" src="https://github.com/user-attachments/assets/df32100e-a87a-458b-80ae-bad4019a6416" />

## Overview

**AR Sandbox Game** is an interactive mixed-reality game where physical sand acts as a dynamic controller for a digital racing environment.

Using an **Azure Kinect RGBD camera**, the system captures depth data in real-time, generates a 3D mesh, and projects a racing track directly onto the sand. Players manipulate the sand to build ramps or dig tunnels, modifying the terrain instantly to help their vehicle reach checkpoints while avoiding obstacles.

<img width="630" height="386" alt="Undistortion" src="https://github.com/user-attachments/assets/811e1b33-da56-4dc1-86cd-3e6145a6f36f" />
---

## Key Features

### Gameplay Mechanics
* **Real-time Terrain Modification:** The game world updates instantly as players dig or pile sand.
* **Dual Modes:**
    * **Player Mode:** Manual vehicle control via keyboard (WASD).
    * **AI Mode:** Autonomous bot navigation using **NavMesh** pathfinding.
* **Dynamic Checkpoints:** Checkpoints visually adapt to terrain height requirements:
    * 🟦 **Blue:** Checkpoint is below current sand level (requires digging).
    * 🟪 **Pink:** Checkpoint is above current sand level (requires piling sand).

<img width="593" height="381" alt="Sandbox" src="https://github.com/user-attachments/assets/7f27e921-f3ad-4716-94c6-7942b889d36c" />
<img width="565" height="401" alt="Checkpoints" src="https://github.com/user-attachments/assets/8959fa93-ad8c-4d60-a24b-75467dc68cfa" />

### Technical Highlights
* **Depth Data Processing:** Conversion of raw depth maps to undistorted bird's-eye view images using OpenCV.
* **Hand Detection & Inpainting:** Algorithms automatically identify and remove player hands from the depth map to prevent artifacts in the terrain mesh.
<img width="604" height="166" alt="Depth data processing" src="https://github.com/user-attachments/assets/b1589fbe-004f-4d8f-890f-cbd597f93e82" />
* **Procedural Mesh Generation:** Custom tessellation solution replacing standard Unity Terrain for high-speed updates.
<img width="600" height="213" alt="Tree" src="https://github.com/user-attachments/assets/ddd8c45c-d24f-4a7d-a99f-7ff082685fc5" />

---

## Performance Optimization

One of the project's critical achievements was optimizing the heavy Computer Vision and Mesh Generation pipeline to run in real-time.

| Metric | Initial Performance | Optimized Performance |
| :--- | :--- | :--- |
| **FPS** | 1–3 FPS | **110–150 FPS** |
| **Lag** | Noticeable freezes | Smooth Real-time |

**Optimization Strategies:**
1.  **Multithreading with UniTask:** Heavy computations (Undistortion, Depth Reading) were offloaded to parallel threads using `UniTask.RunOnThreadPool`.
2.  **Frame Yielding:** Implemented `UniTask.Yield()` to distribute heavy calculations across multiple frames, preventing main thread blocking.
3.  **GPU Acceleration:** Utilized Compute Shaders for grass rendering and efficient terrain tessellation.

<img width="600" height="277" alt="Shader Optimization" src="https://github.com/user-attachments/assets/6e19c398-8143-4fc6-a682-c191c919a8d5" />

---

## System Architecture

The data pipeline processes raw sensor input into a playable 3D mesh:

```text
[Azure Kinect] --> [Depth Data Capture] 
                        |
                        v
                 [OpenCV Processing]
            (Undistortion -> Crop -> Hand Masking)
                        |
                        v
                 [Mesh Generation] <----(UniTask / Multithreading)
            (Heightmap -> Tessellation -> Normals Recalculation)
                        |
                        v
                 [Unity Game Logic]
    (NavMesh AI + Car Physics + Projection Mapping)
```

# Contributors & Roles

This project was developed by a team of students. Roles are defined based on the project workload report.

| Developer            | Key Responsibilities |
|---------------------|--------------------|
| Timur Gildeev       | Mesh tessellation/generation, Core Optimization (Async/Await/UniTask), Point cloud & Height map processing, Car mechanics, Environment assets, Physics tuning |
| Bulat Fakhrutdinov  | Mesh generation logic, Shader optimization, Car spawn system, Computer Vision (Undistortion), Pathfinding integration, UI/Minimap |
| Andrei Salov        | Backend logic, Checkpoints system, Race services (Game Loop) |
| Thomas Kühn         | Hand detection algorithms, Hole filling, Depth data processing |

## External Assets & Dependencies

**Note:** This project utilizes specific packages from the Unity Asset Store to handle vehicle physics, advanced terrain rendering, and async operations. Some of these assets may not be included in the repository due to licensing restrictions.

| Asset Name | Usage | Link |
| :--- | :--- | :--- |
| **Race Car Package** | Base model and physics controller for the vehicle | [Asset Store](https://assetstore.unity.com/packages/3d/vehicles/race-car-package-141690) |
| **Fantasy Landscape** | Environment assets (Trees, Rocks, Terrain textures) | [Asset Store](https://assetstore.unity.com/packages/3d/environments/fantasy-landscape-103573) |
| **Procedural Grass & Tessellation** | GPU-accelerated grass rendering and terrain tessellation | [Asset Store](https://assetstore.unity.com/packages/vfx/shaders/procedural-grass-gpu-shader-terrain-tessellation-tools-274414) |
| **Point Grass Renderer** | Optimized rendering for dense vegetation | [Asset Store](https://assetstore.unity.com/packages/3d/vegetation/point-grass-renderer-207854) |
| **UniTask** | Efficient allocation-free async/await integration for Unity | [GitHub](https://github.com/Cysharp/UniTask) |

---

# Installation & Requirements

## Hardware
- **Camera:** Azure Kinect DK (RGBD)  
- **Projector:** Any standard projector (BenQ recommended)  
- **Sandbox:** Physical box with kinetic sand  

## Software
- **Unity:** 2022.3+  
- **OS:** Windows 10/11 (Required for Kinect Drivers)  

## Setup
1. Clone the repository.  
2. Connect Azure Kinect to PC.  
3. Calibrate the projector using the built-in "Red Cross" calibration scene to align the digital projection with the physical box.  
4. Run **Start Scene**.  

<div align="center">

# 📍 INHA Localization
**Probabilistic Self-Localization for Humanoid Soccer**

[![ROS2](https://img.shields.io/badge/ROS2-Humble-3490dc.svg?style=for-the-badge&logo=ros&logoColor=white)](https://docs.ros.org/en/humble/)
[![C++](https://img.shields.io/badge/C++-17-00599C.svg?style=for-the-badge&logo=c%2B%2B&logoColor=white)](https://en.cppreference.com/w/cpp/17)
[![License](https://img.shields.io/badge/License-Apache_2.0-yellow.svg?style=for-the-badge)](LICENSE)

*Robust Global Localization • Symmetry-Aware Estimation • Vision-Odometry Fusion*

</div>

---
## Problem Statement

**Humanoid soccer localization is inherently ill-posed.**

Compared to wheeled robots, humanoid platforms operate under more challenging conditions:
- Significant odometry drift caused by foot slippage and impacts  
- Highly ambiguous visual observations due to **field symmetry**  
- Sparse and occasionally unreliable landmarks under occlusion and motion blur  

In RoboCup-style soccer fields, it is common for multiple robot poses to be
consistent with the same set of visual observations.
A reliable localization system should therefore be able to:
1. **Maintain multiple pose hypotheses**
2. **Resolve symmetric ambiguities over time**
3. **Remain stable under partial or noisy measurements**

The **INHA Localization** module is designed with these requirements in mind,
and adopts a **probabilistic, multi-hypothesis estimation framework**.

---

## Core Algorithm Overview

INHA Localization is based on an **adaptive particle filter (SIR)** that integrates:
- Odometry-based motion prediction  
- Vision-based field marker observations  
- Assignment-aware likelihood evaluation  
- Temporal consistency mechanisms  

Instead of converging to a single pose prematurely, the system maintains
multiple hypotheses and gradually refines them as reliable evidence becomes available.

---

## Key Design Choices & Differentiators

### Assignment-Aware Measurement Modeling

Detected field markers (L, T, X, goal posts) are associated with known map landmarks
using a **global one-to-one assignment formulation**, solved via the
**Hungarian algorithm**.

- Prevents duplicated assignments to the same landmark  
- Naturally supports partial and unordered observations  

Measurement likelihood is evaluated using a
**distance-anisotropic Gaussian error model**:
- Residuals are decomposed into **radial** and **tangential** components  
- Measurement uncertainty increases with landmark distance  

This approach enables robust and geometry-aware likelihood evaluation,
even when observations are sparse or partially ambiguous.

---

### Adaptive Hypothesis Management via ESS-Controlled Resampling

To maintain a well-conditioned particle set, the filter continuously monitors
the **Effective Sample Size (ESS)**.

- Resampling is performed only when weight degeneracy is detected  
- Multi-modal hypotheses are preserved under symmetric uncertainty  
- Efficient convergence is achieved once observations become informative  

---

### Reference-Guided Pose Finalization with Temporal Gating

Final pose estimation is formulated as a **temporal consistency problem**.

- **Initialization**: the highest-weight particle is selected to establish a
  stable initial reference  
- **Tracking**: particles are filtered using position and orientation gating
  relative to the previous estimate  
- **Estimation**: a weighted mean is computed when sufficient consensus exists;
  otherwise, the system falls back to the maximum-weight hypothesis  

The final pose is further stabilized using **orientation gating** and
**exponential moving average (EMA) smoothing**, allowing symmetric ambiguities
to be resolved gradually over time rather than instantaneously.

---
## Performance Evaluation

Localization accuracy was evaluated using **Absolute Pose Error (APE)** with  
**SE(3) Umeyama alignment** in half court condition.

<p align="center">
  <img src="images/localization_evo.png" width="360"/><br>
</p>

### Δ Translation Error (meters)

| Metric | Value |
|------|------:|
| Mean | **0.17 m** |
| Median | 0.15 m |
| RMSE | 0.19 m |
| Max | 0.54 m |

> Evaluation over **153,555 poses**,
> **455 s duration**

---
## System Architecture

<img src="images/localization_architecture.png" width="800"/>

---

<div align="center">
    <b>Built by INHA United</b><br>
    <i>Pushing the boundaries of Autonomous Soccer</i>
</div>

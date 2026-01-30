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

**Humanoid soccer localization is fundamentally ill-posed.**

Unlike wheeled robots, humanoids experience:
- Large odometry drift due to foot slippage and impacts
- Highly ambiguous observations caused by **field symmetry**
- Sparse and intermittent landmarks under occlusion and motion blur

In RoboCup-style soccer fields, multiple robot poses can explain the same set of visual observations.
A robust localization system must therefore:
1. **Maintain multiple pose hypotheses**
2. **Resolve symmetric ambiguities over time**
3. **Remain stable under partial or unreliable observations**

The **INHA Localization** module is designed to explicitly address these challenges using a **probabilistic, multi-hypothesis framework**.

---

## Core Algorithm Overview

INHA Localization adopts an **adaptive particle filter (SIR)** that fuses:
- **Odometry-driven motion prediction**
- **Vision-based field marker observations**
- **Assignment-aware likelihood modeling**
- **Temporal consistency through clustering and smoothing**

Rather than collapsing to a single pose prematurely, the system maintains and evaluates a diverse hypothesis set until sufficient evidence accumulates.

---
## Key Design Choices & Differentiators

### ▸ Assignment-Aware Measurement Modeling

Visual observations of field markers (L, T, X, goal posts) are associated with the known field map using a **global one-to-one assignment formulation**.

- Marker correspondence is solved via the **Hungarian algorithm**
- Prevents duplicated assignment of multiple observations to a single landmark
- Naturally handles partial and unordered observations

Given an assignment, each particle evaluates measurement likelihood using a **distance-anisotropic Gaussian model**:
- Errors are decomposed into **radial (normal)** and **tangential (perpendicular)** components
- Measurement uncertainty scales with landmark distance

This formulation reflects the physical characteristics of vision-based localization:
- Angular uncertainty dominates at long range
- Radial consistency is more informative than lateral alignment

As a result, likelihood evaluation remains **geometrically meaningful and robust** under cluttered or sparse observations.

---

### ▸ Adaptive Hypothesis Management via ESS-Controlled Resampling

To maintain a healthy hypothesis set, the filter continuously monitors **Effective Sample Size (ESS)**.

- Resampling is triggered only when weight degeneracy is detected
- Prevents premature collapse under symmetric ambiguity
- Enables rapid concentration once observations become informative

This adaptive strategy allows the system to **preserve multi-modal pose hypotheses** during global uncertainty, while still converging decisively when evidence accumulates.

---

### ▸ Reference-Guided Pose Finalization with Temporal Gating

Final pose estimation is formulated as a **temporal consistency problem**, rather than a spatial clustering task.

**Bootstrap phase**  
When no prior pose exists, the system commits to the **single highest-weight particle** to establish an initial reference pose, avoiding unstable averaging under symmetry.

**Tracking phase**  
Once a reference pose is available:
- Particles are filtered using **position and orientation gates** relative to the previous estimate
- If the accumulated weight of inlier particles exceeds a minimum threshold, the pose is computed via **weighted mean aggregation**
- Otherwise, the system falls back to the maximum-weight hypothesis

The final pose is further stabilized using:
- **Orientation gating** to reject inconsistent hypotheses
- **Exponential moving average (EMA)** smoothing for temporal continuity

This design enforces stability without assuming spatial density, enabling **robust symmetry resolution through time rather than instantaneous clustering**.

---

## Performance Evaluation

Localization accuracy was evaluated using **Absolute Pose Error (APE)** with  
**SE(3) Umeyama alignment**.

<table>
<tr>
<td width="55%" valign="top" align="center">

<img src="images/localization_evo.png" width="500"/><br>
<em>APE over time under SE(3) Umeyama alignment.</em>

</td>
<td width="45%" valign="top">

<b>Δ Translation Error (meters)</b>

<table>
<tr><th align="left">Metric</th><th align="right">Value</th></tr>
<tr><td>Mean</td><td align="right"><b>0.17 m</b></td></tr>
<tr><td>Median</td><td align="right">0.15 m</td></tr>
<tr><td>RMSE</td><td align="right">0.19 m</td></tr>
<tr><td>Max</td><td align="right">0.54 m</td></tr>
</table>

</td>
</tr>
</table>

> Evaluation over **153,555 poses**,  
> **203 m trajectory**, **455 s duration**

---

## System Architecture

> 📌 *Architecture diagram will be inserted here.*

<!-- Image placeholder -->

---

## Summary

**INHA Localization** prioritizes:
- Robustness over premature certainty
- Explicit handling of ambiguity
- Stability under real humanoid motion

By combining principled probabilistic modeling with domain-specific geometric insight,
the system delivers reliable localization suitable for high-level decision-making
in dynamic humanoid soccer environments.

---

<div align="center">
    <b>Built by INHA United</b><br>
    <i>Pushing the boundaries of Autonomous Soccer</i>
</div>

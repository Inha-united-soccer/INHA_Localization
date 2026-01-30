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

### 1️⃣ Marker-Aware Data Association (Hungarian Assignment)

Visual observations of field markers (L, T, X, Goal posts) are associated with the known field map using a **global assignment formulation**.

- One-to-one correspondence enforced via the **Hungarian algorithm**
- Prevents duplicated assignment of multiple observations to a single landmark
- Naturally handles partial observations

This design avoids brittle nearest-neighbor matching and stabilizes likelihood computation under clutter.

---

### 2️⃣ Distance-Anisotropic Likelihood Model

Each particle evaluates observation likelihood using an **anisotropic Gaussian error model**:

- Errors decomposed into:
  - **Normal (radial)** component
  - **Perpendicular (tangential)** component
- Variance scales with landmark distance

This reflects the physical reality that:
- Angular uncertainty dominates at long range
- Lateral misalignment is less informative than radial consistency

The result is a likelihood function that is both **geometry-aware** and **distance-robust**.

---

### 3️⃣ ESS-Guided Resampling with Degeneracy Control

To prevent particle impoverishment:
- Effective Sample Size (ESS) is monitored every update
- Resampling is triggered only when weight degeneracy is detected

This allows the filter to:
- Preserve multi-modal hypotheses when ambiguity exists
- Aggressively concentrate when observations become informative

---

### 4️⃣ Symmetry Resolution via Weight-Gated Clustering

Instead of directly averaging all particles, pose extraction follows a **DBSCAN-like clustering strategy**:

- Clustering performed around the previous pose estimate
- Distance and orientation gates applied
- Only clusters exceeding a minimum total weight are considered valid

If no stable cluster forms, the system gracefully falls back to the highest-weight hypothesis.

This mechanism enables **temporal symmetry breaking** without ad-hoc rules.

---

### 5️⃣ Temporal Consistency with Orientation Gating & EMA Smoothing

To suppress sudden pose flips:
- Orientation gating rejects hypotheses inconsistent with recent estimates
- Final pose output is smoothed using an **exponential moving average (EMA)**

This ensures:
- Stable downstream control
- Reduced jitter under noisy observations

---

## Performance Evaluation

Localization accuracy was evaluated using **Absolute Pose Error (APE)** with **SE(3) Umeyama alignment**.

### 📐 Translation Error (meters)

| Metric | Value |
|------|------|
| Mean | **0.17 m** |
| Median | **0.15 m** |
| RMSE | **0.19 m** |
| Max | 0.54 m |

### 🔄 Rotation Error (degrees)

| Metric | Value |
|------|------|
| Median | **9.21°** |
| Mean | 47.4° |
| RMSE | 85.7° |

> Evaluation over **153,555 poses**,  
> **203 m trajectory**, **455 s duration**

Large rotation outliers correspond to intentional symmetry flips early in localization,
which are resolved as more observations accumulate.

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
    <b>Built with by INHA United</b><br>
    <i>Pushing the boundaries of Autonomous Soccer</i>
</div>

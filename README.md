# GRAVITAS

### Gravity Sensitivity Screening Engine for Space Biology & Tissue Engineering

<p align="center">
  <strong>A deterministic multiphysics engine + Physics-Informed Neural Network for screening whether gravity changes are biologically meaningful.</strong>
</p>

<p align="center">
  <a href="https://github.com/Tanish-str/Gravitas/actions">
    <img src="https://img.shields.io/badge/CI-GitHub%20Actions-blue?logo=githubactions" alt="CI">
  </a>
  <img src="https://img.shields.io/badge/Python-3.9%2B-blue?logo=python" alt="Python 3.9+">
  <img src="https://img.shields.io/badge/PyTorch-2.0%2B-ee4c2c?logo=pytorch" alt="PyTorch">
  <img src="https://img.shields.io/badge/Streamlit-Dashboard-ff4b4b?logo=streamlit" alt="Streamlit">
  <img src="https://img.shields.io/badge/License-MIT-yellow.svg" alt="MIT License">
</p>

---

## Overview

**GRAVITAS** is a computational screening engine for **space biology and tissue-engineering experiments**.

The central question is:

> **When gravity changes, does the biology change because of gravity-driven transport, or does diffusion dominate anyway?**

GRAVITAS connects external transport physics with internal biological reaction-diffusion behavior. It combines **dimensionless fluid-dynamics analysis**, a **1D reaction-diffusion solver**, **Michaelis-Menten kinetics**, and a **Physics-Informed Neural Network (PINN)** to estimate how a biological experiment may respond under different gravity environments.

The intended outcome is a practical pre-flight screening gate:

**PASS → MARGINAL → FAIL**

This can help identify experiments where a gravity-dependent effect is large enough to justify further experimental investigation before committing payload mass, launch opportunities, and laboratory resources.

---

## Why GRAVITAS?

Space-biology experiments can be expensive and operationally constrained. A useful screening tool should answer three questions early:

| Question | GRAVITAS Approach |
|---|---|
| Does gravity alter the transport regime? | Dimensionless-number analysis |
| Does transport alter the biological concentration field? | Reaction-diffusion solver |
| Is the predicted change meaningful? | Verdict-based sensitivity screening |

### Core concept

**Gravity change → Transport regime → Nutrient availability → Biological sensitivity**

---

## Key Features

### Multiphysics Gravity Screening

GRAVITAS evaluates mechanisms including:

- Buoyancy-driven convection
- Sedimentation / Stokes behavior
- Multiphase separation
- Hydrostatic pressure effects
- Gravity-dependent membrane transport

It uses dimensionless groups such as:

- **Grashof number**
- **Péclet number**
- **Bond number**

to determine whether a gravity change is likely to push an experiment across a meaningful physical regime boundary.

---

### Reaction-Diffusion Modeling

The biological layer models spatial concentration gradients inside a cell aggregate using a **1D steady-state reaction-diffusion formulation** with **Michaelis-Menten kinetics**.

This provides an interpretable bridge between:

**external transport physics**  
↓  
**internal biological state**

---

### Physics-Informed Neural Network

GRAVITAS includes a PyTorch-based **Physics-Informed Neural Network (PINN)** designed to approximate reaction-diffusion solutions across a continuous parameter space.

The training pipeline uses **Latin Hypercube Sampling (LHS)** across the model's dimensionless parameters.

The objective is to provide a fast surrogate for repeated screening and design-space exploration while retaining the governing physics within the training loss.

---

### Data Provenance

Physical parameters are maintained through a centralized registry so that model inputs remain traceable to their documented sources.

This improves:

- Reproducibility
- Auditability
- Scientific transparency
- Future model extension

---

### Interactive Dashboard

A Streamlit dashboard provides:

- Experiment configuration selection
- Target-gravity input
- PASS / MARGINAL / FAIL screening
- Mechanism ranking
- Concentration-profile visualization
- Parameter provenance

---

# System Architecture

```text
                ┌────────────────────────────┐
                │   Experiment Configuration │
                │        YAML / Inputs       │
                └─────────────┬──────────────┘
                              │
                              ▼
                ┌────────────────────────────┐
                │     Physics Classifier     │
                │ Grashof / Peclet / Bond   │
                │ Sedimentation / Pressure  │
                └─────────────┬──────────────┘
                              │
                        Sensitivity
                              │
                              ▼
                ┌────────────────────────────┐
                │ Reaction-Diffusion Solver  │
                │ Finite Difference + SciPy │
                │ Michaelis-Menten kinetics │
                └─────────────┬──────────────┘
                              │
                       Reference solution
                              │
                ┌─────────────▼──────────────┐
                │       PINN Surrogate       │
                │        PyTorch Model       │
                │       LHS Training         │
                └─────────────┬──────────────┘
                              │
                              ▼
                ┌────────────────────────────┐
                │       Verdict Engine       │
                │   PASS / MARGINAL / FAIL  │
                └─────────────┬──────────────┘
                              │
                              ▼
                ┌────────────────────────────┐
                │     Streamlit Dashboard    │
                │ Rankings + Profiles + Data │
                └────────────────────────────┘
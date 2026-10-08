# GRAVITAS

## Gravity Sensitivity Screening Engine for Space Biology & Tissue Engineering

GRAVITAS is a deterministic computational multiphysics engine combined with a Physics-Informed Neural Network (PINN) for evaluating whether changes in gravity can produce biologically meaningful effects in space-based biological experiments.

The system connects external transport physics with internal biological reaction-diffusion behavior to provide an early computational screening mechanism before an experiment is committed to a flight or laboratory campaign.

---

## Overview

A fundamental question in space biology is:

> **When gravity changes, does the biology change because of gravity-driven transport, or does diffusion dominate regardless of the gravity environment?**

GRAVITAS approaches this problem by combining:

- Dimensionless fluid-dynamics analysis
- Gravity-dependent transport mechanisms
- 1D reaction-diffusion modeling
- Michaelis-Menten kinetics
- Physics-Informed Neural Networks
- Numerical validation
- Experiment configuration through YAML
- Interactive Streamlit visualization

The engine evaluates an experiment and produces a screening verdict:

```text
PASS
MARGINAL
FAIL
```

This provides a computational pre-flight screening gate for identifying experiments that are potentially sensitive to changes in gravity.

---

## Core Concept

```text
                     Gravity Environment
                              │
                              ▼
                     Transport Physics
                              │
                 ┌────────────┴────────────┐
                 │                         │
                 ▼                         ▼
            Convection                Sedimentation
                 │                         │
                 └────────────┬────────────┘
                              ▼
                     Nutrient Transport
                              │
                              ▼
                    Reaction-Diffusion
                              │
                              ▼
                     Biological Response
                              │
                              ▼
                       GRAVITAS Verdict
                     PASS / MARGINAL / FAIL
```

---

## Key Features

### 1. Multiphysics Gravity Screening

GRAVITAS evaluates physical mechanisms that can change when the gravity environment changes.

The engine considers:

- Buoyancy-driven convection
- Sedimentation
- Stokes settling behavior
- Multiphase separation
- Hydrostatic pressure
- Gravity-dependent transport

Dimensionless groups are used to characterize the physical regime, including:

- Grashof Number
- Péclet Number
- Bond Number

These quantities help determine whether a change in gravity can move the system across an important transport-regime boundary.

### 2. Reaction-Diffusion Modeling

The biological component models spatial concentration gradients inside a biological aggregate.

The current formulation uses:

- 1D steady-state reaction-diffusion model
- Finite-difference numerical approach
- Michaelis-Menten reaction kinetics
- Nonlinear numerical solving

The objective is to determine whether changes in transport conditions produce a meaningful change in the internal biological concentration profile.

### 3. Physics-Informed Neural Network

GRAVITAS includes a PyTorch-based Physics-Informed Neural Network (PINN).

The PINN is trained to approximate the underlying reaction-diffusion solution across a continuous parameter space.

The training pipeline uses Latin Hypercube Sampling (LHS) to efficiently sample the model's dimensionless parameters.

The PINN training process is:

```text
Parameter Space
      │
      ▼
Latin Hypercube Sampling
      │
      ▼
Dimensionless Parameters
      │
      ▼
Physics-Informed Loss
      │
      ▼
Neural Network Optimization
      │
      ▼
Trained PINN Surrogate
```

The trained surrogate can then be used for faster exploration of the experiment design space.

### 4. Experiment Configuration

Experiments are defined using YAML configuration files.

Example experiment files are stored inside:

```text
examples/
```

This allows biological experiments to be modified without changing the core engine implementation.

A configuration can define parameters such as:

- Aggregate geometry
- Biological species
- Diffusivity
- Reaction parameters
- Bulk concentration
- Membrane permeability
- Physical properties

### 5. Verdict Engine

The final screening stage compares the predicted biological response under different gravity conditions.

The engine produces one of three outcomes:

#### PASS

The predicted gravity-dependent effect is below the defined sensitivity threshold.

#### MARGINAL

The predicted effect is close to the sensitivity threshold and requires further investigation.

#### FAIL

The predicted gravity-dependent effect is sufficiently significant to warrant experimental consideration.

---

## System Architecture

```text
                    ┌─────────────────────────┐
                    │ Experiment YAML Config  │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │    Experiment Loader    │
                    │       / Parameters      │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │    Physics Classifier   │
                    │                         │
                    │ Grashof / Péclet / Bond │
                    │ Sedimentation / Pressure│
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │    Reaction-Diffusion   │
                    │     Numerical Solver    │
                    │                         │
                    │ Finite Difference       │
                    │ Michaelis-Menten        │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │      PINN Surrogate     │
                    │                         │
                    │ PyTorch + LHS Sampling  │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │      Verdict Engine     │
                    │                         │
                    │ PASS / MARGINAL / FAIL  │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │    Streamlit Dashboard  │
                    │                         │
                    │ Rankings / Profiles /   │
                    │ Experiment Results      │
                    └─────────────────────────┘
```

---

## Project Structure

```text
Gravitas/
│
├── .github/
│   └── workflows/              # GitHub Actions / CI
│
├── examples/
│   └── *.yaml                  # Experiment configurations
│
├── src/
│   └── gravitas/
│       ├── ai/
│       │   └──                 # PINN architecture and physics loss
│       │
│       ├── mechanisms/
│       │   └──                 # Gravity / transport mechanisms
│       │
│       ├── registry/
│       │   └──                 # Physical constants and provenance
│       │
│       └── solver/
│           └──                 # Reaction-diffusion and kinetics
│
├── tests/
│   └──                         # Physics and numerical validation
│
├── dashboard.py                # Streamlit dashboard
├── train_pinn.py               # PINN training pipeline
├── pyproject.toml              # Python package configuration
├── README.md
└── LICENSE
```

---

# Installation

## Requirements

GRAVITAS requires:

- Python 3.9 or higher

A Python 3.10+ environment is recommended.

### Main Dependencies

- NumPy
- SciPy
- PyYAML
- PyTorch
- Matplotlib
- Streamlit
- PyTest

## Clone the Repository

```bash
git clone https://github.com/Tanish-str/Gravitas.git
cd Gravitas
```

## Install the Core Engine

```bash
pip install -e .
```

## Install Development Dependencies

```bash
pip install -e ".[dev]"
```

## Install AI Dependencies

```bash
pip install -e ".[ai]"
```

## Install Everything

For development, dashboard usage, testing, and PINN training:

```bash
pip install -e ".[dev,ai]"
```

---

# Running GRAVITAS

There are three primary ways to use the system:

1. Interactive Dashboard
2. PINN Training Pipeline
3. Python API

---

## 1. Run the Interactive Dashboard

The Streamlit dashboard provides an interactive interface for configuring and screening experiments.

Run:

```bash
streamlit run dashboard.py
```

The application will start locally at:

```text
http://localhost:8501
```

### Dashboard Features

The dashboard provides:

- Experiment selection
- Target gravity input
- Experiment summary
- Screening verdict
- Mechanism ranking
- Concentration profiles
- Parameter provenance

### Dashboard Workflow

```text
Select Experiment
        │
        ▼
Load YAML Configuration
        │
        ▼
Select Target Gravity
        │
        ▼
Run GRAVITAS Screening
        │
        ▼
Calculate Mechanism Sensitivity
        │
        ▼
Solve Reaction-Diffusion Model
        │
        ▼
Display Verdict
        │
        ├── PASS
        ├── MARGINAL
        └── FAIL
```

---

## 2. Run the PINN Training Pipeline

The Physics-Informed Neural Network can be trained across a continuous parameter space using:

```bash
python train_pinn.py
```

The training process:

1. Initializes the PINN architecture.
2. Generates Latin Hypercube Samples.
3. Samples the model parameter space.
4. Calculates the physics-informed loss.
5. Performs neural-network optimization.
6. Tracks training convergence.
7. Saves the trained model.

The trained model is saved as:

```text
surrogate_pinn.pth
```

The training script also generates a convergence plot:

```text
pinn_training_curve.png
```

The PINN is intended to provide a faster surrogate representation of the reaction-diffusion solution for exploration of the experiment parameter space.

---

## 3. Run an Experiment Through the Python API

GRAVITAS can also be integrated into other Python-based computational workflows.

Example:

```python
from gravitas.experiment import Experiment
from gravitas.verdict import screen

# Load biological configuration
exp = Experiment.from_yaml("examples/reference_case.yaml")

# Screen for Lunar Gravity (0.16g)
# Standard g = 9.80665 m/s²
# Moon ≈ 1.62 m/s²
verdict = screen(exp, target_g=1.62)

print(f"Result: {verdict.result}")
print(f"Limiting Factor: {verdict.limiting_reason}")
```

---

# Running an Experiment

Experiment configurations are stored in:

```text
examples/
```

For example:

```text
examples/
├── reference_case.yaml
└── ...
```

To run an experiment through the dashboard:

```bash
streamlit run dashboard.py
```

Then select the desired experiment configuration from the dashboard.

The system loads the YAML configuration and evaluates the experiment against the selected gravity environment.

---

## Example Screening Scenario

Consider a biological aggregate being evaluated under Lunar gravity.

### Standard Earth Gravity

```text
g = 9.80665 m/s²
```

### Lunar Gravity

```text
g ≈ 1.62 m/s²
```

The experiment can be evaluated using:

```python
verdict = screen(
    experiment,
    target_g=1.62
)
```

GRAVITAS then evaluates the physical and biological response and returns:

```text
Result: PASS
```

or:

```text
Result: MARGINAL
```

or:

```text
Result: FAIL
```

The verdict also identifies the limiting mechanism responsible for the result.

---

# Validation and Testing

GRAVITAS includes automated tests for the numerical and physical behavior of the engine.

Run the complete test suite using:

```bash
pytest tests/ -v
```

The tests are intended to validate:

- Mathematical limits
- Numerical solver behavior
- Physical relationships
- Reaction-diffusion convergence
- Deterministic behavior

---

# Technical Stack

| Component | Technology |
|---|---|
| Programming Language | Python |
| Numerical Computing | NumPy |
| Scientific Computing | SciPy |
| Configuration | YAML / PyYAML |
| Machine Learning | PyTorch |
| Sampling | SciPy QMC / Latin Hypercube Sampling |
| Visualization | Matplotlib |
| Dashboard | Streamlit |
| Testing | PyTest |
| Packaging | setuptools |
| CI | GitHub Actions |

---

# Design Philosophy

## Physics First

The neural network is not intended to replace the underlying physical model.

Instead, the PINN learns the governing behavior represented by the physics-based formulation.

## Reproducibility

The same experiment configuration and model parameters should produce reproducible screening results.

## Traceability

Physical parameters and constants should remain traceable to their documented sources.

## Computational Screening

GRAVITAS is designed to provide an early computational assessment before committing an experiment to an expensive laboratory or flight campaign.

---

# Research Context

GRAVITAS combines four major areas:

```text
Space Biology
      +
Computational Physics
      +
Numerical Modeling
      +
Scientific Machine Learning
```

The system is particularly relevant to biological experiments involving:

- Cell aggregates
- Organoids
- Tissue engineering
- Nutrient transport
- Gravity-dependent transport
- Spaceflight biological experiments

---

# Current Scope

The current implementation focuses on:

- Gravity-dependent transport mechanisms
- Dimensionless physical analysis
- 1D reaction-diffusion modeling
- Michaelis-Menten kinetics
- Biological concentration gradients
- PINN-based surrogate modeling
- Experiment configuration through YAML
- Interactive screening through Streamlit

---

# Future Extensions

The architecture can be extended to support:

- Time-dependent reaction-diffusion models
- Additional biological species
- More complex biological kinetics
- 2D and 3D geometries
- Additional gravity environments
- Experimental calibration datasets
- Larger biological parameter spaces
- Automated experiment optimization
- Hardware and flight experiment data integration

---

# Development

Create a feature branch:

```bash
git checkout -b feature/your-feature
```

Make your changes:

```bash
git add .
```

Commit:

```bash
git commit -m "Add your change"
```

Push:

```bash
git push origin feature/your-feature
```

Then open a pull request against:

```text
main
```

---

# License

This project is licensed under the MIT License.

See the `LICENSE` file for details.

---

# Repository

**GRAVITAS**

https://github.com/Tanish-str/Gravitas

---

**GRAVITAS — Screen the physics before you fly the biology.**

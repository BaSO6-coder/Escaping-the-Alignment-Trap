# Escaping the Alignment Trap: Physics-Guided Safe Reinforcement Learning for Power Grid Restoration

[![Conference](https://img.shields.io/badge/Submitted%20to-KDD%202026-blue)](https://kdd.org/kdd2026/)
[![Status](https://img.shields.io/badge/Status-Under%20Double--Blind%20Review-orange)]()
[![License](https://img.shields.io/badge/License-MIT-green)]()

> ⚠️ **Notice on Code Release & Anonymity**
>
> To strictly comply with the KDD double-blind review policy, the full source code, pre-trained PPO agent weights, and HMM-generated scenario datasets are temporarily withheld in this repository. 
>
> **The complete codebase (including Grid2Op environments, MILP oracle scripts, and training pipelines) will be open-sourced immediately upon acceptance.**

---

## 📖 Overview

This repository contains the official implementation plan and diagnostic tools for the paper: **"Escaping the Alignment Trap: Physics-Guided Safe Reinforcement Learning for Power Grid Restoration"**.

### The Problem: The "Alignment Trap"
In multi-objective reinforcement learning for safety-critical systems, linear scalarization often creates degenerate landscapes where the trivial "no-action" policy becomes a stable attractor. We term this structural pathology the **Alignment Trap**.

When safety penalties are dense and instantaneous, agents often converge to a **"safe inaction" equilibrium** (e.g., leaving a power grid blacked out to trivially avoid line overload risks), failing to perform the intended task.

### Our Solution
We propose a **Physics-Guided Diagnostic Framework** that resolves this pathology through:
1.  **Diagnostic Checklist**: A formalized criterion to detect if a Constrained MDP is prone to policy collapse.
2.  **Hybrid EV-CVaR Objective**: A novel reward formulation that mathematically restores "Gradient Traction" to escape the inaction basin.
3.  **Physics-Guided Distillation**: A pipeline transferring offline MILP oracle safety manifolds to real-time PPO agents ($<10$ ms latency).
4.  **Broad Gini Metric**: A composite metric unifying efficiency, resilience, and equity.

---

## 🖼️ Framework Architecture

![Physics-Guided Framework](https://via.placeholder.com/800x400.png?text=Upload+Figure+1+or+Figure+8+Here+to+Show+Architecture)

*Figure: The three-stage architecture transferring offline optimization (System 2) to real-time control (System 1).*

---

## 🧰 Diagnostic Tool: Are You in the Trap?

One of the core contributions of this work is a diagnostic checklist for safety-critical RL. We provide the mathematical criteria to determine if your environment suffers from the Alignment Trap:

* **Check 1: Trivial Feasibility** ($\pi_{\emptyset}$)
    * Does a "do-nothing" policy (e.g., zero action) trivially satisfy all safety/fairness constraints?
* **Check 2: Gradient Dominance** ($\lambda ||\nabla J_C|| \gg ||\nabla J_U||$)
    * Do constraint violations create steep, dense penalty gradients that overwhelm utility gradients near the trivial policy?
* **Check 3: Instantaneous Penalization**
    * Are safety/fairness metrics evaluated per-step rather than on realized trajectory outcomes?

**If all three checks pass, your system is structurally vulnerable to policy collapse.** Our Hybrid EV-CVaR objective is designed to break Condition 2 and 3.

---

## 🧪 Experimental Environments

Our framework is validated on high-fidelity power grid simulators and generalized to other domains.

### Primary Testbed: Power Grid Restoration
* **Simulator**: [Grid2Op](https://github.com/grid2op/grid2op) with `LightSim2Grid` backend.
* **Topologies**: IEEE 30-bus (Standard), IEEE 118-bus (Large-scale).
* **Data**: NREL Phoenix Solar Irradiance Data (Stochastic scenarios via HMM).

### Generalizability Domains (Appendix F)
We also demonstrate the universality of the Alignment Trap in:
* **Financial Risk Control**: Credit scoring under strict default penalties.
* **Medical Triage**: ICU admission under demographic parity constraints.
* **Traffic Signal Control**: Intersection management under collision avoidance penalties.

---

## 📂 Planned Repository Structure

Upon release, the repository will be organized as follows to ensure reproducibility:

```text
.
├── agents/
│   ├── ppo_student.py       # PPO agent with action masking (System 1)
│   ├── milp_oracle.py       # Pyomo/CBC implementation (System 2)
│   └── hybrid_reward.py     # Hybrid EV-CVaR objective implementation
├── envs/
│   ├── grid2op_wrapper.py   # Custom reward wrappers and observation normalization
│   └── domains/             # Minimal envs for Finance, Medical, and Traffic tasks
├── data/
│   └── hmm_scenarios/       # Pre-generated stochastic solar scenarios
├── evaluation/
│   ├── broad_gini.py        # Script to calculate Efficiency-Resilience-Equity metrics
│   └── alignment_check.py   # Diagnostic script for gradient dominance
└── train.py                 # Main entry point for distillation training

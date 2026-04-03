# HEA-TI & VQEs — Variational Quantum Eigensolver for Molecular Ground States

> **MSc Physics Research Project · IIT Kharagpur · 2026**  
> **Author:** Prashant Gupta (Roll No. 24PH40033)  
> **Supervisor:** Prof. Sonjoy Majumder, Department of Physics  
> **Hardware platform focus:** Trapped-Ion Quantum Computers  

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Scientific Background](#2-scientific-background)
3. [Repository Structure](#3-repository-structure)
4. [Notebook Guide](#4-notebook-guide)
   - [File 1 — Baseline Comparison](#file-1--baseline-comparison)
   - [File 2 — Improved HEA-TI](#file-2--improved-hea-ti)
   - [File 3 — Multi-Optimiser Benchmark](#file-3--multi-optimiser-benchmark)
   - [File 4 — Noisy HEA-TI Simulation](#file-4--noisy-hea-ti-simulation)
   - [Direction 1 — Learnable td Study](#direction-1--learnable-td-study)
   - [Direction 1 — Extended Final](#direction-1--extended-final)
   - [Direction 1 — Publication Upgrades v2](#direction-1--publication-upgrades-v2)
   - [Direction 2 — Per-Layer td Study](#direction-2--per-layer-td-study)
5. [Key Results](#5-key-results)
6. [The HEA-TI Ansatz](#6-the-hea-ti-ansatz)
7. [Molecules Studied](#7-molecules-studied)
8. [Software Stack](#8-software-stack)
9. [How to Run](#9-how-to-run)
10. [Citation](#10-citation)

---

## 1. Project Overview

This repository contains the complete computational research for an MSc thesis on the **Variational Quantum Eigensolver (VQE)** applied to molecular ground-state energy calculations, with a specific focus on the **Hardware-Efficient Ansatz for Trapped Ions (HEA-TI)**. The work progresses in four distinct stages and two original research directions:

```
Stage 1 (File 1)  →  Baseline comparison: UCCSD vs TwoLocal vs HEA-TI
Stage 2 (File 2)  →  Improved HEA-TI: gradient-based optimisation + warm-starting
Stage 3 (File 3)  →  Five-optimiser benchmark at increased circuit depth
Stage 4 (File 4)  →  Noise-aware density-matrix simulation → paradox discovered

Direction 1       →  Characterising learnable evolution time (td) in HEA-TI
                      → 4/4 molecule prediction accuracy at zero extra gate cost

Direction 2       →  Per-layer td schedules: does more flexibility help?
                      → Active investigation
```

The **central original contribution** of this project is the **Expressivity Gap** predictor:

$$\Delta E_{\text{gap}} = E_{\text{fixed}}^{\text{ideal}} - E_{\text{learn}}^{\text{ideal}} \quad [\text{mHa}]$$

A single cheap noiseless VQE run computes this quantity, which then predicts whether making $t_d$ learnable will *help* or *hurt* on noisy hardware — **without running any noise simulation**. This predictor achieves **100% accuracy across four molecules** spanning a wide range of electronic structure complexity.

---

## 2. Scientific Background

### The Problem

Finding the ground-state energy of a molecule is exponentially hard classically (Full Configuration Interaction scales as $\mathcal{O}(K^N)$ for $N$ electrons in $K$ orbitals). Quantum computers offer a natural encoding: $n$ qubits span the full $2^n$-dimensional fermionic Fock space.

### VQE: The Algorithm

The **Variational Quantum Eigensolver** (Peruzzo et al., 2014) is a hybrid quantum-classical algorithm:

```
 ┌─────────────────────────────────────────────────────────────┐
 │  Classical computer                                          │
 │  • Choose parameters θ                                      │
 │  • Run optimiser (L-BFGS-B / COBYLA / ADAM ...)            │
 │  • Receive energy E(θ) → update θ                          │
 └──────────────────────────┬──────────────────────────────────┘
                            │
 ┌──────────────────────────▼──────────────────────────────────┐
 │  Quantum processor                                           │
 │  • Prepare ansatz state |ψ(θ)⟩ = U(θ)|HF⟩                 │
 │  • Measure E(θ) = ⟨ψ(θ)|Ĥ|ψ(θ)⟩                          │
 └─────────────────────────────────────────────────────────────┘
```

By the **variational principle**, $E(\theta) \geq E_0$ always, so minimising $E(\theta)$ gives the best approximation to the true ground-state energy $E_0$.

### Why Trapped Ions?

Trapped-ion processors achieve the highest reported two-qubit gate fidelities (~99.95–99.97%) and all-to-all qubit connectivity via phonon-mediated interactions. Their native entangling operation is a **global XY spin-spin interaction**:

$$H_{XY} = \sum_{i < j} J_{ij} \left( \sigma_i^x \sigma_j^x + \sigma_i^y \sigma_j^y \right), \qquad J_{ij} \propto |i-j|^{-1.5}$$

The **HEA-TI ansatz** (Zhuang, Wu & Duan, PRA 2024) is built directly around this native interaction, making it hardware-efficient in the truest sense — no decomposition into hardware-unfriendly gates is needed.

### The td Paradox

In File 4 (noisy simulation), we discovered a striking result at circuit depth $D=4$ under depolarising noise:

| Molecule | Fixed $t_d = 0.4$ | Learnable $t_d$ | Verdict |
|---|---|---|---|
| H₂ | **2.58 mHa** | 20.52 mHa | Fixed wins (8× better) |
| LiH | 29.87 mHa | **1.05 mHa** | Learnable wins (28× better) |

Same circuit. Same noise. Same depth. Completely opposite outcomes. **Direction 1** resolves this paradox.

---

## 3. Repository Structure

```
HEA-TI & VQEs/
│
├── 📓 File 1_VQE_Final_Complete.ipynb           # Stage 1: Baseline comparison
├── 📓 File 2_VQE_Final_Complete_Improved.ipynb  # Stage 2: Improved HEA-TI
├── 📓 File 3_VQE_Final_Complete_HEA-TI.ipynb   # Stage 3: Optimiser benchmark
├── 📓 File 4_VQE_Noisy_HEA-TI.ipynb            # Stage 4: Noisy simulation + td paradox
│
├── 📓 Direction1_Learnable_td_Study_v2 Final.ipynb  # Dir 1: Core study
├── 📓 Direction1_Extended_Final.ipynb               # Dir 1: Extended (8 seeds, BeH₂, H₂O)
├── 📓 Direction1_Publication_Upgrades_v2.ipynb      # Dir 1: 7 publication-grade fixes
│
├── 📓 Direction2_PerLayer_td_Study.ipynb        # Dir 2: Per-layer td schedule study
│
├── 📓 H2_VQE_Fixed_Complete.ipynb              # Early standalone H₂ notebook
│
├── 📄 Direction1_Extended_Report.pdf           # Direction 1 research report
├── 📄 Direction1_Report.pdf                    # Direction 1 initial report
├── 📄 code report.pdf                          # Code walkthrough report
├── 📄 paper report.pdf                         # Theoretical paper analysis
│
└── 📋 README.md                                # This file
```

---

## 4. Notebook Guide

### File 1 — Baseline Comparison

**`File 1_VQE_Final_Complete.ipynb`**

The starting point. Establishes a three-ansatz comparison for H₂ and LiH using **COBYLA** (gradient-free) optimisation.

| What it does | Details |
|---|---|
| Molecules | H₂ (4 qubits, 11 bond distances) · LiH (6 qubits, 6 bond distances, active space 2e/3o) |
| Ansätze | UCCSD · TwoLocal ($L=2$) · HEA-TI ($D=4$, fixed $t_d=0.4$) |
| Optimiser | COBYLA (gradient-free, 1000 max evaluations) |
| Reference | Exact FCI via `NumPyMinimumEigensolver` |

**Key findings:**

```
                H₂ (mean error, mHa)   LiH (mean error, mHa)   LiH pass rate
UCCSD           0.000                  0.132                    6/6  ✓
TwoLocal L=2    0.052                  3.538                    5/6  ~
HEA-TI D=4      1.327                  27.451                   0/6  ✗
```

HEA-TI completely fails for LiH. The investigation of why — and how to fix it — drives all subsequent work.

---

### File 2 — Improved HEA-TI

**`File 2_VQE_Final_Complete_Improved.ipynb`**

Tests the two hypotheses from File 1: (a) gradient-free optimiser is insufficient; (b) circuit depth D=4 is too shallow for LiH.

| What it changes | File 1 → File 2 |
|---|---|
| Optimiser | COBYLA → **L-BFGS-B** (gradient-based) |
| HEA-TI depth for H₂ | D=4 → **D=6** |
| HEA-TI depth for LiH | D=4 → **D=8** |
| Initialisation | Cold start → **warm-starting** along PES |
| Max iterations | 1000 → 2000 |

**Key findings:**
- LiH HEA-TI: **27.451 mHa → 0.000 mHa** (complete recovery, 6/6 geometries within chemical accuracy)
- H₂ HEA-TI: sub-microhartree accuracy at all geometries
- The File 1 failure was *correctable*, not fundamental

---

### File 3 — Multi-Optimiser Benchmark

**`File 3_VQE_Final_Complete_HEA-TI.ipynb`**

With the ansatz fixed at D=6 (H₂) and D=8 (LiH), systematically benchmarks five classical optimisers.

| Optimiser | Type | H₂ error (mHa) | LiH error (mHa) |
|---|---|---|---|
| ADAM | Gradient | 0.0000 | 0.0000 |
| L-BFGS-B | Gradient | 0.0002 | 0.0000 |
| CG | Gradient | 0.0003 | 0.0001 |
| SLSQP | Gradient | 0.1285 | 0.0216 |
| COBYLA | Gradient-free | 0.1352 | 0.3018 |

All five achieve chemical accuracy (< 1.6 mHa) at sufficient depth. **L-BFGS-B and CG** converge fastest. **COBYLA** is the slowest but remains viable for gradient-free hardware settings.

---

### File 4 — Noisy HEA-TI Simulation

**`File 4_VQE_Noisy_HEA-TI.ipynb`**

Introduces **depolarising noise** via Qiskit Aer density-matrix simulation. The pivotal notebook — it discovers the $t_d$ paradox.

| Setting | Value |
|---|---|
| Noise model | Depolarising: $p_1 = 0.1\%$ (1-qubit), $p_2 = 1\%$ (2-qubit CX) |
| Simulation | `AerEstimatorV2`, `method='density_matrix'`, 4096 shots |
| Seeds | 4 independent random initialisations |
| Ablation | Depth $D \in \{2, 4, 6, 8\}$ × mode $\in$ \{fixed $t_d$, learnable $t_d$\} |

The D=4 result is the central paradox that motivates Direction 1. Fixed and learnable $t_d$ circuits have **identical CX gate counts** — only one extra classical variable differs.

---

### Direction 1 — Learnable td Study

**`Direction1_Learnable_td_Study_v2 Final.ipynb`**

The core Direction 1 notebook. Defines the **Expressivity Gap** and runs the initial 4-seed experiments.

$$\boxed{\Delta E_{\text{gap}} = E_{\text{fixed}}^{\text{ideal}} - E_{\text{learn}}^{\text{ideal}}}$$

- Computed from a **single noiseless VQE run**
- Predicts whether learnable $t_d$ helps or hurts on noisy hardware
- Requires **no noise simulation** at all

| Molecule | $\Delta E_\text{gap}$ (mHa) | Prediction | Verified? |
|---|---|---|---|
| H₂ | −20.10 | Learnable **HURTS** | ✓ |
| LiH | +24.90 | Learnable **HELPS** | ✓ |
| BeH₂ | +42.23 | Learnable **HELPS** | ✓ |
| H₂O | +67.53 | Learnable **HELPS** | ✓ |

**4/4 = 100% prediction accuracy.**

---

### Direction 1 — Extended Final

**`Direction1_Extended_Final.ipynb`**

Expands the core study with:
- **8 seeds** (up from 4) for robust statistics
- Full **3-experiment structure**: Experiment B (noiseless) · Experiment A (8-level noise sweep) · Experiment C (4-molecule generalisation)
- LiH **PES sweep** across multiple bond distances
- **Leave-one-out cross-validation** ($R^2 = 0.97$, threshold $\Delta E^*_\text{gap} = 7.83$ mHa)
- **Convergence curve analysis** (8 seeds per configuration)

Saves checkpoint files:
- `direction1_expAB_checkpoint.json` — noiseless + noise sweep data
- `direction1_expC_checkpoint.json` — 4-molecule generalisation at $p_2 = 0.001\%$

**Strongest individual result: BeH₂**
- Fixed $t_d$: 44.02 mHa → Learnable $t_d$: 1.34 mHa
- **33× improvement** at zero additional gate cost

---

### Direction 1 — Publication Upgrades v2

**`Direction1_Publication_Upgrades_v2.ipynb`**

Seven targeted fixes that strengthen the Direction 1 results toward journal submission:

| Fix | What was corrected | Key result |
|---|---|---|
| **Fix 1** | LiH PES stopped at 3.0 Å | Crossover at $R^* = 2.980$ Å (ionic → covalent) |
| **Fix 2** | Gradient evaluated at wrong point | $R^2 = 0.199$; gradient is **not** a predictor (retracted) |
| **Fix 3** | No quantitative convergence metrics | $P_\text{esc} = 1.0$ for LiH/BeH₂/H₂O in ~100 evals |
| **Fix 4** | AD+PD noise was 570× too strong | 4/4 accuracy under calibrated $\gamma_{2q} = 7.03 \times 10^{-6}$ |
| **Fix 5** | Oracle scan used different budget | Learnable $t_d$ uses **28–30× fewer evaluations** |
| **Fix 6** | No CIs on regression | Slope = 1.203 [0.574, 1.831], $R^2 = 0.971$ |
| **Fix 7** | H₂ fragility not quantified | $P_\text{esc} = 1/8$ per seed; ~32 seeds to escape wrong attractor |

Saves output JSONs and publication-quality figures.

---

### Direction 2 — Per-Layer td Study

**`Direction2_PerLayer_td_Study.ipynb`**

The latest and most advanced notebook. Asks whether a **layer-dependent schedule** $(t_d^{(0)}, t_d^{(1)}, \ldots, t_d^{(D-1)})$ outperforms a single shared learnable $t_d$.

Three modes are compared at identical CX gate count:

```
fixed      →  all layers use t_d = 0.4     (0 extra classical params)
global     →  one shared learnable t_d      (1 extra classical param)
layerwise  →  one t_d per layer             (D extra classical params)
```

**Key diagnostic metrics:**
- `DeltaE_sched` = improvement from global → layerwise (the new Direction 2 quantity)
- `eta_nonuniform` = std of the learned schedule (does the optimizer use the extra freedom?)
- `Pearson r(eta, DeltaE_sched)` = does schedule diversity predict gain?

**Experiments:**
1. **Ideal noiseless benchmark** at D=4 across all 4 molecules
2. **Low-noise benchmark** at $p_2 = 0.001\%$ across all 4 molecules
3. **Depth scaling** (D = 2, 4, 6): global vs layerwise
4. **Optional LiH geometry pilot**: does the schedule change with bond distance?

> ⚠️ This notebook produces its results via checkpoint JSONs
> (`direction2_ideal_checkpoint.json`, `direction2_noisy_checkpoint.json`,
> `direction2_depth_checkpoint.json`). Run sequentially in Colab.

---

## 5. Key Results

### Direction 1 Summary Table

| Molecule | Fixed $t_d$ error (mHa) | Learnable $t_d$ error (mHa) | Improvement | Chemical accuracy? |
|---|---|---|---|---|
| H₂ | **2.41** | 21.19 | −8.8× (worse) | Fixed: ✓ Learn: ✗ |
| LiH | 30.74 | **1.56** | +19.7× | Learn: ✓ Fixed: ✗ |
| BeH₂ | 44.02 | **1.34** | +32.9× | Learn: ✓ Fixed: ✗ |
| H₂O | 71.34 | **3.16** | +22.6× | Learn: ~ Fixed: ✗ |

*All at $p_2 = 0.001\%$, $D=4$, 8 seeds. Chemical accuracy = 1.6 mHa.*

### The Expressivity Gap Ordering

$$\Delta E_\text{gap}(\text{H}_2) = -20.10 < 0 < +24.90 = \Delta E_\text{gap}(\text{LiH}) < +42.23 = \Delta E_\text{gap}(\text{BeH}_2) < +67.53 = \Delta E_\text{gap}(\text{H}_2\text{O})$$

The gap grows **monotonically with molecular complexity** and is **stable across two orders of magnitude in noise level** — no crossover is observed between $p_2 = 0.0001\%$ and $p_2 = 0.02\%$.

### Hardware Target

Learnable $t_d$ achieves chemical accuracy for LiH at:

$$p_2 \leq 10^{-5} \quad \Longleftrightarrow \quad \mathcal{F}_\text{CX} \geq 99.999\%$$

Current best reported: IonQ Forte ≈ 99.97%, Quantinuum H2 ≈ 99.95%.

---

## 6. The HEA-TI Ansatz

The circuit structure for $D$ layers:

$$U_\text{HEA-TI}(\boldsymbol{\theta}, t_d) = \prod_{d=1}^{D} \left[ e^{-i t_d H_{XY}} \cdot \prod_{i} R_Z(\theta_{d,i}) \right] \cdot U_\text{HF}$$

```
|HF⟩ ─── Rz(θ₀₀) ─┬── Rz(θ₁₀) ─┬── ... ─ Rz(θ_{D-1,0}) ─┬──
|HF⟩ ─── Rz(θ₀₁) ─│── Rz(θ₁₁) ─│── ... ─ Rz(θ_{D-1,1}) ─│──
|HF⟩ ─── Rz(θ₀₂) ─┤── Rz(θ₁₂) ─┤── ... ─ Rz(θ_{D-1,2}) ─┤──
         ...        │            │                           │
              e^{-i·td·HXY}  e^{-i·td·HXY}            e^{-i·td·HXY}
              (layer 1)       (layer 2)                  (layer D)
```

**Key property:** All three $t_d$ modes — fixed, global, layerwise — produce circuits with **identical CX gate counts** at matched depth. Only the number of classical optimisation variables changes.

| Mode | Extra classical params | Extra CX gates | Effect |
|---|---|---|---|
| Fixed $t_d = 0.4$ | 0 | 0 | $t_d$ compiled into gate matrix |
| Global learnable | +1 (shared across all layers) | 0 | Single $t_d$ as Qiskit Parameter |
| Per-layer learnable | +$D$ (one per layer) | 0 | Layer-dependent schedule |

---

## 7. Molecules Studied

| Molecule | Formula | Qubits | Active space | Geometry | $E_0$ (Ha) |
|---|---|---|---|---|---|
| Hydrogen | H₂ | 4 | Full STO-3G | $r = 0.74$ Å | −1.13728383 |
| Lithium hydride | LiH | 6 | 2e, 3o | $r = 1.505$ Å | −7.86442412 |
| Beryllium dihydride | BeH₂ | 6 | 2e, 3o | Linear, $r_\text{Be-H} = 1.33$ Å | −15.56065015 |
| Water | H₂O | 6 | 2e, 3o | $r_\text{O-H} = 0.9572$ Å, ∠HOH = 104.52° | −74.96450886 |

All calculations use the **STO-3G minimal basis set** and the **Jordan–Wigner mapping**. The Active Space Transformer (2e, 3o) freezes the $1s^2$ core of the heavy atom and retains the chemically relevant valence orbitals, reducing LiH/BeH₂/H₂O from 12–14 qubits to 6 qubits.

---

## 8. Software Stack

| Package | Version | Role |
|---|---|---|
| `qiskit` | 2.3.1 | Circuit construction, transpilation, parameterisation |
| `qiskit-aer` | latest | Density-matrix simulator, depolarising noise models |
| `qiskit-nature` | latest | Molecular Hamiltonians, JW mapping, active space |
| `qiskit-algorithms` | latest | VQE driver, optimiser interfaces |
| `pyscf` | latest | Molecular integral engine (HF, MO coefficients) |
| `numpy` | 2.0.2 | Numerical arrays, RNG |
| `scipy` | latest | Optimiser backends, statistics |
| `matplotlib` | latest | Figures and visualisation |
| `pylatexenc` | latest | LaTeX rendering in plots |

**Runtime environment:** Google Colab (Python 3.12)

---

## 9. How to Run

### Prerequisites

All notebooks are designed to run on **Google Colab**. Each notebook begins with an installation cell:

```python
import subprocess, sys
packages = ['qiskit[visualization]', 'qiskit-aer', 'qiskit-nature[pyscf]',
            'qiskit-algorithms', 'pyscf', 'numpy', 'scipy', 'matplotlib', 'pylatexenc']
for pkg in packages:
    subprocess.check_call([sys.executable, '-m', 'pip', 'install', '-q', pkg])
```

Run this cell **once per Colab session** before anything else.

### Recommended Run Order

```
1. File 1_VQE_Final_Complete.ipynb          (~20–30 min)
2. File 2_VQE_Final_Complete_Improved.ipynb (~30–40 min)
3. File 3_VQE_Final_Complete_HEA-TI.ipynb  (~30–40 min)
4. File 4_VQE_Noisy_HEA-TI.ipynb           (~45–60 min, density-matrix)
5. Direction1_Learnable_td_Study_v2 Final.ipynb    (~60 min)
6. Direction1_Extended_Final.ipynb                 (~2–3 hrs, 8 seeds × 4 mols)
7. Direction1_Publication_Upgrades_v2.ipynb        (~1–2 hrs, requires checkpoints)
8. Direction2_PerLayer_td_Study.ipynb              (~2–4 hrs)
```

### Checkpoint Files

Direction 1 and Direction 2 notebooks write JSON checkpoint files after completing each experiment. These allow you to:
- Resume an interrupted run without recomputing everything
- Load results for plotting only, without re-running VQE
- Share raw numerical data independently of the notebook

Key checkpoint files produced:

```
direction1_expAB_checkpoint.json   ← Exp B (noiseless gaps) + Exp A (noise sweep)
direction1_expC_checkpoint.json    ← Exp C (4-molecule generalisation)
direction2_ideal_checkpoint.json   ← Direction 2 ideal D=4 benchmark
direction2_noisy_checkpoint.json   ← Direction 2 low-noise benchmark
direction2_depth_checkpoint.json   ← Direction 2 depth scaling (D=2,4,6)
```

### Running Direction 2 Standalone

Cell 8 of `Direction2_PerLayer_td_Study.ipynb` can **self-bootstrap** in a fresh Colab session without re-running the full pipeline. Simply:

1. Run Cell 1 (package installation)
2. Jump directly to Cell 8

The cell detects the fresh environment and rebuilds all required helpers and molecule data automatically.

---

## 10. Citation

If you use this code or results in your work, please cite:

```bibtex
@mastersthesis{gupta2026vqe,
  author      = {Prashant Gupta},
  title       = {Variational Quantum Eigensolver for Molecular Ground States:
                 From Ideal Simulation to Noise-Aware Trapped-Ion Computation
                 and the Characterisation of Learnable Evolution Time in HEA-TI},
  school      = {Indian Institute of Technology Kharagpur},
  year        = {2026},
  type        = {MSc Thesis},
  department  = {Department of Physics},
  supervisor  = {Prof. Sonjoy Majumder},
}
```

This work is based on the foundational paper:

```bibtex
@article{zhuang2024heati,
  author  = {Zhuang, J.-Z. and Wu, Y.-K. and Duan, L.-M.},
  title   = {Hardware-efficient variational quantum algorithm in a trapped-ion quantum computer},
  journal = {Physical Review A},
  volume  = {110},
  pages   = {062414},
  year    = {2024},
  doi     = {10.1103/PhysRevA.110.062414},
}
```

---

<div align="center">

**Department of Physics · Indian Institute of Technology Kharagpur**  
**MSc Research Project · March 2026**

*"A single cheap noiseless calculation predicts noisy hardware performance with 100% accuracy across four molecules — no noise simulation required."*

</div>

# HEA-TI & VQEs: Hardware-Efficient Variational Quantum Algorithms for Trapped Ions

## Overview

This folder contains comprehensive study materials on **Hardware-Efficient Ansatz for Trapped Ions (HEA-TI)** and **Variational Quantum Eigensolvers (VQEs)** for quantum chemistry applications, specifically focusing on molecular ground-state calculations.

### Based on Research Paper

**"Hardware-efficient variational quantum algorithm in a trapped-ion quantum computer"**  
*J.-Z. Zhuang, Y.-K. Wu, and L.-M. Duan*  
Physical Review A 110, 062414 (2024)

---

## Contents

### 1. **H2_VQE_Fixed_Complete.ipynb**
   - **Type**: Jupyter Notebook (Qiskit Implementation)
   - **Description**: Complete working implementation of VQE for H₂ molecule
   - **Features**:
     - ✅ Three different ansätze: UCCSD, TwoLocal, and HEA-TI
     - ✅ Full compatibility with latest Qiskit versions
     - ✅ Detailed comments and explanations
     - ✅ Chemical accuracy validation
   - **Molecules Covered**: H₂ at equilibrium bond distance (0.74 Å)
   - **Key Results**:
     - UCCSD: 0.0000 mHa error (exact)
     - TwoLocal: 0.0302 mHa error
     - HEA-TI: 0.0131 mHa error
   - **All methods achieve chemical accuracy** (<1.5 mHa)

### 2. **code-report.pdf**
   - **Type**: Technical Documentation
   - **Description**: Cell-by-cell analysis of the Jupyter notebook
   - **Contents**:
     - Theoretical background (VQE, Jordan-Wigner mapping, chemical accuracy)
     - Detailed code explanation for each cell
     - Mathematical derivations
     - Implementation details and compatibility fixes
     - Connection to paper results
     - Troubleshooting guide
   - **Sections**: 8 chapters + appendices

### 3. **paper-report.pdf**
   - **Type**: Graduate-Level Comprehensive Report
   - **Description**: Complete analysis of the HEA-TI research paper
   - **Contents**:
     - **70+ pages** of detailed explanations
     - Quantum mechanics fundamentals
     - Trapped-ion quantum physics
     - Variational quantum algorithms theory
     - HEA-TI circuit construction and universality proofs
     - Optimization strategies (gradient-based, parameter-shift rule)
     - Experimental techniques and error analysis
     - Results for H₂, LiH, and F₂ molecules
   - **Sections**: 13 chapters with mathematical derivations

---

## Quick Start

### Running the Notebook

1. **Install Dependencies**:
   ```bash
   pip install qiskit qiskit-nature qiskit-nature-pyscf qiskit-algorithms pyscf matplotlib numpy scipy
   ```

2. **Open in Google Colab** (recommended) or Jupyter:
   - Upload `H2_VQE_Fixed_Complete.ipynb`
   - Run all cells sequentially
   - Results appear with visualizations

3. **Expected Runtime**: ~2-3 minutes on Google Colab

---

## Key Concepts

### Variational Quantum Eigensolver (VQE)
- **Hybrid quantum-classical algorithm** for finding ground states
- Uses the **variational principle**: E₀ ≤ ⟨ψ(θ)|H|ψ(θ)⟩
- Suitable for **NISQ** (Noisy Intermediate-Scale Quantum) devices

### Hardware-Efficient Ansatz for Trapped Ions (HEA-TI)
- Exploits **native operations** of trapped-ion quantum computers:
  - Single-qubit rotations (Rz)
  - Global spin-spin interactions (XY model)
- **Advantages**:
  - Fewer parameters than UCCSD
  - Native implementation (no compilation overhead)
  - Achieves chemical accuracy with shallow circuits
- **Structure**: D layers of [Rz rotations + Global XY evolution]

### Molecular Systems Studied

| Molecule | Qubits | HEA-TI Depth | Accuracy | Challenge Level |
|----------|--------|--------------|----------|------------------|
| **H₂**   | 4      | D = 4        | ✓ 0.0131 mHa | Easy (weak correlation) |
| **LiH**  | 6      | D = 5        | ✓ 3 mHa      | Moderate (ionic character) |
| **F₂**   | 12     | D = 9        | ✓ 1.5 mHa    | Hard (strong correlation) |

*Chemical accuracy threshold: < 1.5 mHa*

---

## Technical Details

### Implemented Ansätze

1. **UCCSD** (Unitary Coupled-Cluster Singles & Doubles)
   - Gold standard for quantum chemistry
   - Parameters: 3 for H₂
   - Circuit depth: ~20
   - Accuracy: Exact (0 error)

2. **TwoLocal** (Hardware-efficient for superconducting qubits)
   - Rotation blocks: RY + RZ
   - Entangling blocks: CNOT (linear)
   - Parameters: 32 for H₂
   - Accuracy: 0.0302 mHa

3. **HEA-TI** (Paper's novel ansatz)
   - Rotation blocks: RZ only
   - Entangling: Global XY evolution
   - Parameters: 16 for H₂
   - Accuracy: 0.0131 mHa
   - **Native to trapped-ion platforms**

### Optimization
- **Optimizer**: SLSQP (Sequential Least Squares Programming)
- **Gradient computation**: Parameter-shift rule (quantum-compatible)
- **Convergence**: Typically 100-120 iterations
- **Initialization**: Zero for UCCSD, random for others

---

## Results Summary

### H₂ Molecule (Equilibrium: 0.74 Å)

| Method    | Energy (Hartree) | Error (mHa) | Chemical Acc.? | Parameters | Evaluations |
|-----------|------------------|-------------|----------------|------------|-------------|
| **Exact** | -1.13728383      | 0           | ✓              | N/A        | N/A         |
| **UCCSD** | -1.13728383      | 0.0000      | ✓              | 3          | 10          |
| **TwoLocal** | -1.13725363   | 0.0302      | ✓              | 32         | 1890        |
| **HEA-TI** | -1.13727070     | 0.0133      | ✓              | 16         | 716         |

### Key Findings
- ✅ All three methods achieve chemical accuracy
- ✅ HEA-TI balances parameter count and accuracy
- ✅ UCCSD converges fastest (fewest parameters)
- ✅ TwoLocal requires more iterations (more parameters)

---

## Learning Path

### For Beginners
1. Read **code-report.pdf** Sections 1-4 (Background & Setup)
2. Run the **notebook** cell-by-cell
3. Focus on H₂ results (simplest system)

### For Advanced Study
1. Read **paper-report.pdf** Chapters 1-6 (Theory)
2. Study HEA-TI circuit construction (Chapter 6)
3. Understand optimization strategies (Chapter 8)
4. Explore experimental techniques (Chapter 9)

### For Research
1. Full **paper-report.pdf** (70 pages)
2. Mathematical derivations in Appendices
3. Extend notebook to LiH or F₂
4. Implement noise models and error mitigation

---

## Mathematical Foundations

### Jordan-Wigner Transformation
Maps fermionic operators to qubits:
```
âⱼ → (∏ₖ<ⱼ Zₖ) σⱼ⁻  where σ⁻ = (X - iY)/2
```

### XY Hamiltonian (HEA-TI)
```
Hₓᵧ = ∑ᵢⱼ Jᵢⱼ (σᵢ⁺σⱼ⁻ + σᵢ⁻σⱼ⁺)
    = ∑ᵢⱼ Jᵢⱼ (XᵢXⱼ + YᵢYⱼ)
```
With power-law coupling: `Jᵢⱼ = J₀/|i-j|^α` (α = 1.5)

### Variational Principle
```
E₀ = min ⟨ψ(θ)|H|ψ(θ)⟩
      θ
```

---

## Extensions & Future Work

### Possible Enhancements
1. **Add more molecules**: LiH (6 qubits), H₂O (8 qubits), F₂ (12 qubits)
2. **Noise models**: Depolarizing, amplitude damping
3. **Error mitigation**: Zero-noise extrapolation
4. **Advanced optimizers**: Adam, L-BFGS-B
5. **Excited states**: Variational Quantum Deflation (VQD)

### Research Directions
- Benchmark HEA-TI on real trapped-ion hardware (IonQ, Quantinuum)
- Compare with other hardware-efficient ansätze
- Explore barren plateau mitigation
- Develop adaptive layer-wise training

---

## Prerequisites

### Knowledge
- **Physics**: Quantum mechanics, quantum computing basics
- **Chemistry**: Molecular orbitals, electronic structure
- **Math**: Linear algebra, variational calculus
- **Programming**: Python, Jupyter notebooks

### Software
- Python 3.8+
- Qiskit 1.0+ (latest version)
- Qiskit Nature 0.7+
- PySCF (classical quantum chemistry)

---

## References

### Primary Source
- **Zhuang et al.**, "Hardware-efficient variational quantum algorithm in a trapped-ion quantum computer", Phys. Rev. A **110**, 062414 (2024)

### Related Papers
- **Peruzzo et al.**, "A variational eigenvalue solver on a photonic quantum processor", Nat. Commun. **5**, 4213 (2014) - Original VQE
- **McClean et al.**, "Barren plateaus in quantum neural network training landscapes", Nat. Commun. **9**, 4812 (2018)

### Textbooks
- **Nielsen & Chuang**, *Quantum Computation and Quantum Information* (Cambridge, 2010)
- **Szabo & Ostlund**, *Modern Quantum Chemistry* (Dover, 1996)

---

## Citation

If you use these materials, please cite:

```bibtex
@article{zhuang2024hardware,
  title={Hardware-efficient variational quantum algorithm in a trapped-ion quantum computer},
  author={Zhuang, J.-Z. and Wu, Y.-K. and Duan, L.-M.},
  journal={Physical Review A},
  volume={110},
  pages={062414},
  year={2024},
  publisher={APS}
}
```

---

## Author

**Prashant Gupta**  
MSc Physics, Indian Institute of Technology Kharagpur  
*Quantum Computing, Quantum Chemistry, and Quantum Information Science*

---

## License

MIT License - See repository root LICENSE file

---

## Acknowledgments

- Based on the groundbreaking work by **Zhuang, Wu, and Duan (2024)**
- Implemented using **Qiskit** (IBM Quantum)
- Molecular calculations via **PySCF**
- Prepared for graduate-level study and research

---

## Status

✅ **Complete and Working** - All code tested on Google Colab (November 2025)  
✅ **Chemical Accuracy Validated** - All methods meet < 1.5 mHa threshold  
✅ **Comprehensive Documentation** - 70+ pages of detailed analysis

---

*Last Updated: November 27, 2025*

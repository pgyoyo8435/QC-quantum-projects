# ⚛️ Quantum Network Lab

**Quantum Network Lab** is a rigorous, interactive physics simulator that models the foundational protocols and hardware constraints of telecom-grade quantum networks.

It is built as a **single-file, zero-dependency** web application. There is no build step, no framework, and no installation required—just open the file in any modern browser to start exploring quantum key distribution, entanglement routing, and fiber channel noise.

---

## 🔬 Core Physics Models

The simulator steps beyond toy models, utilizing real-world physical constraints and calculations to model network behavior:

* **BB84 Protocol:** Simulates prepare-and-measure QKD, accounting for optical misalignment, detector dark counts, intercept-resend eavesdropping, and asymptotic privacy amplification limits: $f = \max(0, 1 - 2h_2(Q))$.
* **Time-Bin Entanglement:** Models photon pair generation, Franson interferometry visibility, CHSH Bell inequality violation, and BBM92 secret key rates over lossy optical fiber.
* **Multi-Node Routing:** Implements Dijkstra's algorithm for pathfinding, calculating end-to-end fidelity using Werner-state models for entanglement swapping, time-dependent quantum memory decoherence, and LOCC entanglement purification.
* **Fiber Coexistence:** Calculates link budgets for quantum signals sharing fiber with classical WDM traffic. Computes spontaneous Raman scattering floors, direct classical leakage, and O-band vs. C-band attenuation profiles.

## 🏗️ Architecture & Engineering

This project is an exercise in both quantum theory and highly optimized front-end engineering:

* **Zero Dependencies:** 100% Vanilla JavaScript, HTML5, and CSS3.
* **Custom State Management:** Encapsulated within the `QNL` namespace, the architecture cleanly separates the physics engine, UI state, and rendering logic.
* **Performant Visualizations:** All charts, interferograms, and network topologies are drawn natively using the HTML5 Canvas API. To ensure performance, canvas states are cached and redrawn using a debounced execution cycle during window resizes, preventing unnecessary recalculation of quantum math.
* **Responsive UI:** Features a custom dark-mode design system with DOM-injected mobile steppers, ensuring precise control over variables (e.g., picosecond coincidence windows) across all devices.

## 🚀 Quick Start

**To run locally:**

1. Clone this repository or download `quantum_network_lab.html`.
2. Double-click the file to open it in your browser.

**To use the session exporter:**
The lab includes a JSON export tool. Click **"Export JSON report"** in the top navigation bar to download a snapshot of all active physics parameters and run logs for reproducibility.

## 👨‍💻 Author

**Prashant Gupta**


## 📄 License
This project is open-source and available under the MIT License.

This project is open-source and available under the MIT License.

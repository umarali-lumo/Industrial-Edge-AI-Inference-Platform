# Edge AI Pipeline & Feature Engineering

The NeuralGrid intelligence layer is a sophisticated pipeline that transforms raw electrical telemetry into actionable safety predictions.

## 📊 Feature Extraction (14-Point Vector)
To provide the neural network with temporal context, the system extracts 14 features from the incoming telemetry stream:

| Feature | Symbol | Description |
| :--- | :--- | :--- |
| **Primary** | $V, I, P, E, f, PF$ | Raw telemetry from the sensing layer. |
| **Statistical** | $\sigma^2_I$ | Current variance over a 10-sample window. |
| **Temporal** | $dI/dt, dV/dt$ | First-order derivatives for transient detection. |
| **Thermal** | $\sum I^2 t$ | Accumulative thermal index for overheating prediction. |
| **Operational** | $V_{dev}, PF_{var}$ | Deviation from nominal industrial standards. |

---

## 🧠 Neural Network Inference
The MPU executes a custom-built feed-forward neural network implemented in pure NumPy for maximum performance on edge hardware.
- **Architecture:** Multi-layer perceptron with ReLU activation and a Softmax output layer.
- **Classes:** 
  - `0`: NORMAL
  - `1`: LOAD_INCREASE_WARNING
  - `2`: OVERLOAD_RISK
  - `3`: CRITICAL_CONDITION

---

## 🛡️ Deterministic Safety Fusion
While the AI provides probabilistic classification, the system enforces **Hard Deterministic Safety Rules** for zero-fail operation:
1. **Current Limit:** Any current $\geq 14A$ is immediately escalated to `CRITICAL`.
2. **Voltage Integrity:** Severe sags ($V < 205V$) trigger an unconditional emergency shutdown.
3. **Power Factor:** A critical PF drop ($PF < 0.70$) is flagged as a severe anomaly.

**Result:** The final system output is a fusion of the Neural Network's "intuition" and the Safety Engine's "certainty."

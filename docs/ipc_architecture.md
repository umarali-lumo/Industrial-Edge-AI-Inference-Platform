# Inter-Process Communication (IPC) Architecture

NeuralGrid utilizes the **Arduino Uno Q** dual-processor architecture, requiring a robust IPC mechanism to bridge the deterministic MCU with the high-level Linux MPU.

## 🌉 The Arduino RouterBridge
The system employs `Arduino_RouterBridge`, a high-speed communication framework that allows the MCU to invoke Python methods on the MPU as if they were local functions.

### RPC Mechanism: MsgPack over UART
- **Protocol:** MessagePack (MsgPack) is used for binary serialization, providing a lightweight alternative to JSON for inter-core communication.
- **Method Mapping:** The Python engine registers the `predict_json` method, which the C++ firmware calls using `Bridge.call()`.
- **Synchronicity:** The MCU executes a non-blocking call but waits for a deterministic `100ms` window to allow for MPU feature extraction and inference overhead.

---

## 🛠️ Data Handling & Sanitization
One of the primary engineering challenges was ensuring string integrity over the UART link.

### 1. Hardware-Level Filtering
The MCU implements a character-by-character filter to strip `\r` (Carriage Return) bytes, which are often appended by serial monitors and can corrupt JSON parsing in the Python `json.loads()` module.

### 2. Python-Side Resilience
The MPU engine includes a multi-type input handler in `predict_json(*args)` that can process raw bytes, bytearrays, strings, or pre-parsed dictionaries, ensuring the AI service remains online even if the IPC layer sends malformed data.

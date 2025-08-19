# Neural Network Based Quantum State Tomography (PoC)

This repository is a proof-of-concept exploring whether neural networks can be used to perform quantum state tomography (QST) more efficiently and accurately than classical reconstruction algorithms. Quantum state tomography is the process of reconstructing an unknown quantum state from measurement data, which is crucial in validating quantum devices and algorithms.  

The project implements a full pipeline — synthetic quantum data generation, neural network training, and benchmarking against classical tomography methods — to study **fidelity, error rates, and execution time**.

---

## What This Project Does

- Generates synthetic quantum measurement data for an arbitrary number of qubits.
- Trains a deep neural network to predict complex amplitudes of quantum states.
- Implements and compares classical QST methods:
  - Least Squares (LS)
  - Maximum Likelihood Estimation (MLE)
  - Linear Inversion (LI)
- Benchmarks NN vs. classical approaches in terms of:
  - **Fidelity**
  - **Amplitude & phase errors**
  - **Runtime efficiency**
- Runs statistical significance tests (paired t-tests) between methods.

---

## Why This?

Quantum state tomography is traditionally expensive and scales poorly with the number of qubits. This PoC investigates whether **machine learning can offer a viable alternative** by:

- Reducing computational cost
- Handling noisy measurement data more robustly,
- Matching or outperforming classical fidelity.

---

## How It Works

### 1. Data Generation

A `QuantumDataGenerator` class:

- Creates random pure states,
- Applies measurement settings in Pauli bases (`X`, `Y`, `Z`),
- Adds realistic **shot noise** and Gaussian noise,
- Outputs features (measurement probabilities) and labels (true state amplitudes).

### 2. Neural Network Model

The **QuantumStateNN** is a feed-forward neural network with:

- Input: concatenated measurement probabilities,
- Hidden layers: [1024, 512, 256] with SiLU activations and dropout,
- Output: real + imaginary amplitudes of the quantum state,
- Regularization: L1 penalty + gradient clipping,
- Loss: combination of **L1 error + 1 – Fidelity**.

```

Measurements → Dense Layers → (Real, Imag) Amplitudes → Normalization → Predicted State

````

### 3. Classical Tomography Methods

- **Least Squares (LS)**: solves linear inversion problem.
- **Maximum Likelihood Estimation (MLE)**: iteratively projects onto valid density matrices.
- **Linear Inversion (LI)**: reconstructs using Pauli basis expansion.

### 4. Evaluation

- Fidelity, amplitude error, phase error, MSE
- Runtime performance (samples/sec)
- Statistical significance testing (paired t-tests)

---

## Results

### Quantum State Tomography Method Comparison

| Method           | Fidelity | ±Std   | Min    | Amp Error | Phase Err | Time (s) | Samp/s   |
|------------------|----------|--------|--------|-----------|-----------|----------|----------|
| Neural Network   | 0.9570   | 0.0893 | 0.0012 | 0.2863    | 1.5704    | 0.003    | 127770.6 |
| Least Squares    | 0.1175   | 0.0794 | 0.0008 | 0.2667    | 1.5602    | 20.168   | 20.3     |
| Max Likelihood   | 0.0650   | 0.0592 | 0.0002 | 0.2713    | 1.5694    | 438.417  | 0.9      |
| Linear Inversion | 0.1175   | 0.0794 | 0.0008 | 0.2672    | 1.5796    | 13.379   | 30.6     |

---

### Statistical Significance Tests (NN vs Classical)

- **NN vs Least Squares** — p-value: **0.0000** (significant)  
- **NN vs Max Likelihood** — p-value: **0.0000** (significant)  

---

### Summary

- **Best Fidelity:** Neural Network (0.9570)  
- **Fastest Method:** Neural Network (0.003s)  
- **NN vs Classical (LS):** **8.14× fidelity improvement**  

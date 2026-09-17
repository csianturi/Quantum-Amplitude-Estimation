# Quantum Amplitude Estimation

A small, notebook-based demonstration of **Quantum Amplitude Estimation (QAE)** using Qiskit. The example models a simplified ray-tracing question: given a secondary ray with a known probability of hitting a light source, use QAE to recover that probability from the phase of a Grover operator.

The notebook prepares a one-qubit state with a target probability of `0.30`, runs the standard QAE circuit with quantum phase estimation, and estimates the probability from the most frequently observed evaluation-register result.

## What the notebook covers

`QAE.ipynb` builds the algorithm step by step:

1. **State preparation (`A`)** — encodes the target probability \(a\) in a qubit using \(a = \sin^2(\theta)\).
2. **Oracle** — marks the "hit" state \(|1\rangle\) with a phase flip.
3. **Grover operator (`Q`)** — combines the oracle and state-preparation circuit.
4. **Phase estimation** — applies controlled powers \(Q^{2^k}\) to an evaluation register, then an inverse quantum Fourier transform.
5. **Simulation and post-processing** — runs the circuit with Qiskit Aer and converts the dominant measured phase to \(\hat{a} = \sin^2(\hat{\theta})\).

The notebook first uses three evaluation qubits to illustrate the circuit, then repeats the experiment with seven evaluation qubits for finer phase resolution.

## Requirements

- Python 3.10 or later
- Jupyter Notebook or JupyterLab
- `qiskit`
- `qiskit-aer`
- `numpy`
- `matplotlib`

Install the dependencies in a virtual environment:

```bash
python -m venv .venv
```

Activate the environment:

```bash
# Windows PowerShell
.venv\\Scripts\\Activate.ps1

# macOS/Linux
source .venv/bin/activate
```

Then install the packages:

```bash
pip install qiskit qiskit-aer numpy matplotlib jupyter
```

## Run the example

```bash
jupyter notebook QAE.ipynb
```

Run the cells from top to bottom. The final cells display histograms of the phase-estimation register and print the estimated probability alongside the target value of `0.3000`.

## How the probability is encoded

For a target probability \(a\), the notebook computes:

\[
\theta = \arcsin(\sqrt{a})
\]

and prepares the objective qubit with `Ry(2θ)`. Measuring that qubit would return \(|1\rangle\) with probability \(a\). QAE instead estimates \(\theta\) through phase estimation of the Grover operator and converts it back with:

\[
\hat{a} = \sin^2\!\left(\frac{y\pi}{2^m}\right)
\]

where \(m\) is the number of evaluation qubits and \(y\) is the measured phase-estimation integer.

## Notes

This project is an educational simulator example. It uses the conventional phase-estimation-based form of QAE, whose controlled Grover powers become deep as the number of evaluation qubits increases. Results vary slightly between runs because the Aer simulator samples a finite number of shots.

## Reference

- G. Brassard, P. Høyer, M. Mosca, and A. Tapp, *Quantum Amplitude Amplification and Estimation* (2002). https://arxiv.org/abs/quant-ph/0005055

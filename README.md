# Quantum Amplitude Estimation for Ray Tracing

A simple proof of concept for using **Quantum Amplitude Estimation (QAE)** to improve the Monte Carlo portion of a ray tracer. In a conventional path tracer, many secondary rays are sampled to estimate how often they reach a light source. This notebook represents that hit-or-miss event as a quantum amplitude and uses QAE to estimate its probability.

The example deliberately reduces the ray-tracing problem to one qubit: a secondary ray either misses (`|0⟩`) or hits (`|1⟩`) the light source. It prepares a target hit probability of `0.30`, runs the standard QAE circuit with quantum phase estimation, and recovers an estimate from the measured phase.

QAE is relevant because, on suitable fault-tolerant quantum hardware, it can estimate the same Monte Carlo quantity with a quadratic improvement in query complexity: approximately \(O(1/\epsilon)\) oracle uses for error \(\epsilon\), rather than the \(O(1/\epsilon^2)\) samples required by classical Monte Carlo. This notebook is a simulator-based educational demonstration of that idea; it is not a full quantum ray tracer.

## What the notebook covers

`QAE.ipynb` builds the algorithm step by step:

1. **State preparation (`A`)** — encodes a secondary ray's probability \(a\) of hitting a light source in a qubit using \(a = \sin^2(\theta)\).
2. **Oracle** — marks the light-hit state \(|1\rangle\) with a phase flip.
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

This project is an educational simulator proof of concept for the Monte Carlo estimator in a ray-tracing pipeline. It uses the conventional phase-estimation-based form of QAE, whose controlled Grover powers become deep as the number of evaluation qubits increases. Results vary slightly between runs because the Aer simulator samples a finite number of shots.

## Reference

- G. Brassard, P. Høyer, M. Mosca, and A. Tapp, *Quantum Amplitude Amplification and Estimation* (2002). https://arxiv.org/abs/quant-ph/0005055

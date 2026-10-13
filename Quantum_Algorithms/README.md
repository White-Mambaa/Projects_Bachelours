# Quantum Approximate Optimization Algorithm (QAOA)
## Design, Mathematical Analysis, and Software-Based Evaluation for Combinatorial Optimization

> **Quantum Algorithms (QT789_D) · Level 7 · Four-month course project · Software-only**
>
> **Status:** Proposal drafted; awaiting faculty-guide review and approval. No implementation or experimental findings are claimed at this stage.

## Project at a glance

This project investigates the **Quantum Approximate Optimization Algorithm (QAOA)** as a computational approach to combinatorial optimization. We will formulate a graph optimization problem mathematically, simulate the associated quantum circuits on classical computers, and evaluate how circuit depth, parameter optimization, and graph structure affect solution quality and computational cost.

Our primary benchmark is **Max-Cut**, including unweighted and weighted graphs. The work combines quantum algorithm theory, linear algebra, circuit design, software engineering, classical optimization, and reproducible experimentation. It is a **course project**, not an independent PhD dissertation or a quantum hardware development program.

**Current stage:** Establishing the scope and seeking approval of the project proposal. The implementation plan below is provisional and will be refined with the faculty guide.

## 1. Why this project?

Many computer science problems require choosing the best configuration among an enormous number of possibilities. Examples include partitioning networks, assigning resources, scheduling tasks, and designing efficient systems. These problems are often described as *combinatorial optimization*.

QAOA is a hybrid quantum–classical method: a parameterized quantum circuit prepares a distribution over candidate solutions, and a classical optimizer adjusts the circuit parameters to improve the expected objective value. Its behavior depends on the structure of the problem, the circuit architecture, and the optimization procedure. Understanding these dependencies is as important as producing a good answer on a single example.

This project asks a focused question:

> **How do QAOA circuit depth, parameter selection, and graph structure influence optimization quality and computational efficiency on Max-Cut instances?**

We will not assume that QAOA is faster or better than classical methods. Any comparative conclusion must follow from clearly defined baselines, theoretical reasoning, and reproducible measurements.

## 2. A simple explanation: the Max-Cut problem

Imagine a network in which vertices represent computers and edges represent connections. We want to divide the vertices into two groups so that the total weight of connections crossing between the groups is as large as possible.

For example, if two connected vertices are assigned to different groups, their connecting edge contributes to the cut. If they are assigned to the same group, it does not. **Max-Cut** asks for the assignment with the highest total contribution.

For a graph with \(n\) vertices, there are \(2^n\) binary assignments (or \(2^{n-1}\) distinct partitions when exchanging group names is treated as the same partition). Exhaustive enumeration quickly becomes expensive as \(n\) grows. QAOA offers a structured way to generate and tune a probability distribution over possible assignments, although it does not guarantee the optimum.

## 3. Project goals

1. **Build the mathematical foundation.** Explain quantum states, tensor products, operators, unitary evolution, quantum measurement, and the complexity assumptions relevant to the algorithm.
2. **Formulate Max-Cut as a Hamiltonian problem.** Connect the classical cut objective to a diagonal quantum observable and verify the equivalence.
3. **Design QAOA circuits.** Implement the cost and mixing unitaries, parameterized layers, and computational-basis measurement.
4. **Develop a software reference implementation.** Create an independently verifiable statevector implementation before relying on higher-level quantum SDKs.
5. **Study algorithmic behavior.** Examine circuit depth, parameter initialization, optimization strategies, and graph structure.
6. **Benchmark responsibly.** Compare against exact small-instance solutions, random cuts, and suitable classical heuristics under documented budgets.
7. **Analyze costs and limitations.** Report qubit counts, gate counts, classical simulation requirements, optimization evaluations, and scalability limits.
8. **Deliver reproducible research artifacts.** Publish code, tests, experiment configurations, plots, and a technically rigorous final report, subject to course and supervisor requirements.

## 4. Mathematical formulation

Let \(G=(V,E,w)\) be an undirected weighted graph. For a binary assignment \(z\in\{0,1\}^n\), the weighted Max-Cut objective is

\[
C(z)=\sum_{(i,j)\in E}w_{ij}(z_i\oplus z_j).
\]

Using Pauli-\(Z\) operators, the cost Hamiltonian is

\[
H_C=\frac12\sum_{(i,j)\in E}w_{ij}(I-Z_iZ_j).
\]

It satisfies \(H_C|z\rangle=C(z)|z\rangle\), meaning that the computational-basis eigenvalues are exactly the classical cut values.

The standard transverse-field mixing Hamiltonian is

\[
H_B=\sum_{j=1}^{n}X_j.
\]

A depth-\(p\) QAOA state is

\[
|\psi_p(\boldsymbol\gamma,\boldsymbol\beta)\rangle
=\left[\prod_{\ell=p}^{1}e^{-i\beta_\ell H_B}e^{-i\gamma_\ell H_C}\right]|+\rangle^{\otimes n}.
\]

The algorithm seeks parameters that maximize the expected cut value:

\[
F_p(\boldsymbol\gamma,\boldsymbol\beta)
=\langle\psi_p|H_C|\psi_p\rangle.
\]

For graphs with positive optimal cut value, we can report an expectation-based approximation ratio:

\[
r_p=\frac{F_p}{C_{\max}}.
\]

**Important:** This expected ratio is not identical to the best cut observed in a finite set of measurement shots. We will report these quantities separately.

## 5. What we plan to implement

The intended workflow is:

```text
Graph generation / loading
          |
          v
Exact classical Max-Cut reference (small instances)
          |
          v
Max-Cut cost Hamiltonian
          |
          v
QAOA parameterized quantum circuit
          |
          v
Statevector simulation / expectation estimation
          |
          v
Classical parameter optimizer
          |       ^
          +-------+  (repeat until stopping criterion)
          |
          v
Candidate solutions and performance measurements
          |
          v
Comparison, statistical analysis, and reporting
```

### Software stack

| Component | Proposed tools | Purpose |
|---|---|---|
| Programming | Python | Main research implementation |
| Numerical computing | NumPy, SciPy | Linear algebra and optimization |
| Graph algorithms | NetworkX | Graph generation and classical baselines |
| Quantum simulation | Qiskit or PennyLane (to be selected) | Circuit construction and simulation |
| Visualizations | Matplotlib | Objective landscapes and comparisons |
| Testing | pytest | Correctness and regression checks |
| Execution | Google Colab / Kaggle | Cloud-based software experimentation |

**No physical quantum hardware is required.** A cloud GPU may be used if an appropriate simulation workload benefits from it, but GPU access is not a prerequisite for the baseline study.

## 6. Research questions and evaluation

| Research question | Planned investigation |
|---|---|
| **RQ1 — Mathematical formulation** | Verify that the Max-Cut Hamiltonian reproduces classical objective values and derive the QAOA evolution. |
| **RQ2 — Circuit depth** | Compare low-depth circuits and measure quality improvements against additional computation. |
| **RQ3 — Parameter sensitivity** | Evaluate initialization choices, multiple random starts, and classical optimization methods. |
| **RQ4 — Graph structure** | Compare paths, cycles, regular graphs, random graphs, and weighted instances; explore whether structural information helps parameter selection or transfer. |
| **RQ5 — Classical comparison** | Evaluate QAOA against exact small-instance solutions, random assignments, and selected classical heuristics with explicit resource budgets. |

### Planned metrics

- **Expected cut value:** The mean objective value predicted by the simulated quantum state.
- **Approximation ratio:** Expected cut value divided by the exact Max-Cut value for instances where the optimum is available.
- **Best sampled cut:** Highest cut obtained from a specified number of simulated measurements.
- **Optimization effort:** Objective evaluations, iterations, and wall-clock time.
- **Circuit resources:** Qubits, gate counts, entangling gates, and circuit depth.
- **Scalability:** Memory and runtime behavior as graph size increases.
- **Robustness:** Variation across graph instances, optimizer restarts, and random seeds.

A simulated QAOA circuit running on a classical computer **does not establish practical quantum speedup**. Any claims about advantage will be restricted to what the theoretical and experimental evidence supports.

## 7. Experimental scope

We plan to start with small, exactly verifiable instances and expand only when correctness and computational feasibility are established.

**Initial graph families:** single-edge graphs, paths, cycles, regular graphs, selected random graphs, and positively weighted variants.

**Initial circuit depths:** \(p=1,2,3\), subject to computational limits and guide approval.

**Baseline methods:** exact enumeration for small graphs, uniformly random cuts, and at least one documented classical local-search or approximation heuristic.

**Reproducibility requirements:** fixed and recorded seeds, software versions, graph instance definitions, parameter conventions, optimization budgets, and machine/runtime information.

The project will separate *analytical results*, *verified implementation behavior*, and *empirical observations*. No numerical results are asserted in this README before experiments are run.

## 8. Four-month roadmap

| Phase | Main work | Expected deliverable |
|---|---|---|
| **Month 1 — Foundations and approval** | Finalize guide-approved scope, study mathematical prerequisites, derive the Hamiltonian, and validate an initial reference implementation. | Approved scope, mathematical notes, tested baseline |
| **Month 2 — Circuit implementation** | Implement QAOA circuits, classical optimization loops, and controlled small-instance experiments. | Reproducible QAOA simulation pipeline |
| **Month 3 — Evaluation** | Study graph families, circuit depths, parameter strategies, classical comparisons, and computational resource costs. | Experimental datasets, tables, and figures |
| **Month 4 — Verification and reporting** | Check claims, rerun experiments, document limitations, and prepare the final course report and presentation. | Final technical report, code, and defense materials |

The first action is **faculty-guide approval of the proposal**. Coding and experiments begin only after the topic and scope are approved.

## 9. Planned repository structure

The structure below is a **target layout**, not a statement that the files already exist.

```text
quantum-algorithms-qaoa/
├── README.md
├── proposal/
│   └── project_proposal.md
├── references/
│   ├── octad_reference_map.md
│   └── prior_work_notes.md
├── theory/
│   ├── maxcut_hamiltonian.md
│   └── qaoa_derivations.md
├── src/
│   ├── graphs.py
│   ├── classical_baselines.py
│   ├── hamiltonian.py
│   ├── qaoa.py
│   └── optimization.py
├── tests/
├── notebooks/
├── experiments/
├── results/
├── figures/
├── report/
└── requirements.txt
```

Installation and execution commands will be added after the implementation environment and package versions are finalized. At the proposal stage, there is no runnable application to install.

## 10. The Octad reference framework

The mathematical foundation of the project is governed by the following eight textbooks:

1. **Michael A. Nielsen and Isaac L. Chuang** — *Quantum Computation and Quantum Information*.
2. **Richard J. Lipton and Kenneth W. Regan** — *Quantum Algorithms via Linear Algebra*.
3. **Johannes A. Buchmann** — *Introduction to Quantum Algorithms*.
4. **Eleanor Rieffel and Wolfgang Polak** — *Quantum Computing: A Gentle Introduction*.
5. **N. David Mermin** — *Quantum Computer Science: An Introduction*.
6. **Scott Aaronson** — *Quantum Computing Since Democritus*.
7. **Phillip Kaye, Raymond Laflamme, and Michele Mosca** — *An Introduction to Quantum Computing*.
8. **Mikio Nakahara and Tohru Ohmi** — *Quantum Computing: From Linear Algebra to Physical Realizations*.

The books provide complementary perspectives on mathematical formulation, circuits, computational models, physical interpretations, and complexity boundaries. Specific theorem, chapter, and page references will be recorded only after checking the actual editions. We will not fabricate textbook citations.

QAOA-specific literature will be distinguished from this foundational canon. The original QAOA algorithm was introduced by Farhi, Goldstone, and Gutmann in *A Quantum Approximate Optimization Algorithm* (2014): https://arxiv.org/abs/1411.4028 . Later work will be examined for novelty and experimental context, not substituted for the prescribed foundational texts.

## 11. Scope, exclusions, and academic integrity

### In scope

- Mathematical analysis of QAOA and weighted/unweighted Max-Cut.
- Quantum circuit design and **classical software simulation**.
- Parameter optimization, graph-instance studies, and classical comparisons.
- Formal correctness tests, resource accounting, and reproducible reporting.

### Out of scope

- Physical quantum processor access, hardware engineering, or calibration.
- Claims of demonstrated quantum advantage based solely on classical simulation.
- Unverified claims of a novel theorem or original quantum algorithm.
- Large-scale experiments that exceed the available cloud computing budget.

The project is academically ambitious but deliberately bounded. A careful negative result or a clearly explained limitation is a valid scientific finding.

## 12. Deliverables and completion criteria

The planned final package includes a supervisor-approved proposal, mathematical derivations, verified simulation code, baseline algorithms, automated tests, experiment configurations, plots and tables, a final report, and a presentation.

A successful project must demonstrate:

1. **Correctness:** Core Hamiltonian and circuit identities are derived and tested.
2. **Reproducibility:** Another reader can rerun the documented experiments.
3. **Fair comparison:** Baselines, budgets, and evaluation metrics are explicitly stated.
4. **Interpretability:** The report explains what influences performance and why.
5. **Intellectual honesty:** Results and limitations are presented without exaggerated claims.

## 13. Current status and next step

**Current:** First proposal draft prepared and accepted as the working project direction; faculty-guide approval remains pending.

**Next:** Review the proposal with the faculty guide, incorporate requested changes, freeze the initial experimental scope, and only then begin the mathematical implementation and baseline experiments.

---

### Project summary in one sentence

**We will mathematically derive, simulate, test, and critically evaluate QAOA on Max-Cut to understand how quantum circuit design and classical optimization choices affect solution quality and computational cost—entirely through software.**

*Quantum Algorithms · QT789_D · Level 7 · Proposed four-month course project*

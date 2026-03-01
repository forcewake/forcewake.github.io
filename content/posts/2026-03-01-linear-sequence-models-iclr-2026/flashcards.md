# Architecture Flashcards

## Card 1

**Q:** What are the three core methodological improvements introduced in the Mamba-3 architecture?

**A:** Trapezoidal discretization, complex-valued state updates, and a multi-input multi-output (MIMO) formulation.

---

## Card 2

**Q:** What is the primary benefit of replacing Euler discretization with the trapezoidal rule in Mamba-3?

**A:** It offers a lower sequence approximation error by using a second-order accurate recurrence.

---

## Card 3

**Q:** How does Mamba-3 implement complex-valued dynamics without high computational overhead?

**A:** It uses an equivalence to data-dependent Rotary Embedding (RoPE) rotations on the $B$ and $C$ projections.

---

## Card 4

**Q:** What hardware bottleneck does the Mamba-3 MIMO formulation specifically target?

**A:** The memory-bound nature of decoding in sequence models.

---

## Card 5

**Q:** In the context of Mamba-3, what does 'MIMO' stand for?

**A:** Multi-Input Multi-Output.

---

## Card 6

**Q:** How does the MIMO formulation in Mamba-3 increase 'arithmetic intensity' during decoding?

**A:** It replaces single-vector outer products with matrix-matrix updates.

---

## Card 7

**Q:** What is the 'retrieval gap' observed in pure State Space Models (SSMs) compared to Transformers?

**A:** Fixed-state SSMs generally show weaker information extraction capabilities as context length grows.

---

## Card 8

**Q:** Which architectural strategy is suggested to resolve the retrieval limitations of pure Mamba-3?

**A:** Hybrid architectures that interleave SSM layers with self-attention layers.

---

## Card 9

**Q:** Why were short convolutions removed in the Mamba-3 architecture?

**A:** Trapezoidal discretization and the introduction of $BC$ bias made the explicit convolution layers redundant.

---

## Card 10

**Q:** What is the hypothesized role of the $BC$ bias in Mamba-3?

**A:** It allows the model to run Linear Time-Invariant (LTI) and data-dependent SSMs simultaneously.

---

## Card 11

**Q:** How does the training cost of Mamba-3 MIMO compare to its SISO variant?

**A:** MIMO increases training cost because the training phase is compute-bound rather than memory-bound.

---

## Card 12

**Q:** What does 'SISO' stand for in the context of sequence model variants?

**A:** Single-Input Single-Output.

---

## Card 13

**Q:** What is the 'RoPE trick' used in Mamba-3 complex dynamics?

**A:** Representing complex state updates as rotations applied to the input and output projections.

---

## Card 14

**Q:** According to Mamba-3 evaluations, how does the hybrid model's retrieval performance compare to a pure Transformer?

**A:** The hybrid model can outperform the Transformer baseline on real-world retrieval tasks like SWDE and FDA.

---

## Card 15

**Q:** What specific task is used to demonstrate Mamba-3's superior state-tracking capabilities?

**A:** Formal language tasks such as parity and modular arithmetic.

---

## Card 16

**Q:** What defines 'Error-Free Linear Attention' (EFLA)?

**A:** A linear attention mechanism that uses an exact closed-form solution to the continuous-time dynamics of the delta rule.

---

## Card 17

**Q:** EFLA identifies that the core limitation of existing linear attention methods is low-order _____.

**A:** Discretization

---

## Card 18

**Q:** Which numerical integration scheme is implicitly used by standard DeltaNet?

**A:** The first-order Explicit Euler method.

---

## Card 19

**Q:** How does EFLA achieve linear-time complexity for computing matrix exponentials?

**A:** By leveraging the rank-1 structure of the dynamics matrix $A_t = k_t k_t^T$.

---

## Card 20

**Q:** What is the 'Aha!' moment described in the EFLA paper regarding the dynamics matrix $A_t$?

**A:** The property that $A_t^n = \lambda_t^{n-1} A_t$ for $n \ge 1$, where $\lambda_t = k_t^T k_t$.

---

## Card 21

**Q:** What physical assumption does EFLA use to model continuous signals from discrete sequences?

**A:** The Zero-Order Hold (ZOH) assumption.

---

## Card 22

**Q:** In EFLA, what serves as the 'spectral gate' regulating forgetting and retention?

**A:** The key norm $||k_t||^2$.

---

## Card 23

**Q:** How does a large key norm $||k_t||^2$ affect memory in the EFLA update rule?

**A:** It causes rapid exponential decay of the existing state, clearing memory for new information.

---

## Card 24

**Q:** What happens to the EFLA update rule as the key norm approaches zero?

**A:** It asymptotically recovers the standard delta rule update.

---

## Card 25

**Q:** Why does EFLA require a larger global learning rate compared to DeltaNet?

**A:** To compensate for the exponential saturation effect of the exact decay factor.

---

## Card 26

**Q:** What is the local truncation error order for Euler discretization in linear attention?

**A:** It is $O(\beta_t^2)$.

---

## Card 27

**Q:** Concept: Zero-Order Hold (ZOH)

**A:** Definition: A mathematical model of the practical signal reconstruction done by a digital-to-analogue converter, where the signal is held constant between samples.

---

## Card 28

**Q:** Which benchmark is used to evaluate the modelling capabilities of model architectures through synthetic token manipulation?

**A:** The MAD (Mechanistic Architecture Design) benchmark.

---

## Card 29

**Q:** What is the primary cause of numerical instability in linear attention during long sequences?

**A:** Error accumulation from low-order numerical integration of stiff ordinary differential equations (ODEs).

---

## Card 30

**Q:** How does EFLA's stability compare to DeltaNet in high-interference environments?

**A:** EFLA maintains significantly higher accuracy due to its exact integration mechanism.

---

## Card 31

**Q:** What does the term 'arithmetic intensity' refer to in hardware-efficient model design?

**A:** The ratio of total floating-point operations to the total memory traffic required.

---

## Card 32

**Q:** In the Mamba-3 MIMO variant, what does the rank $r$ represent?

**A:** The number of inputs and outputs processed simultaneously in the state update.

---

## Card 33

**Q:** Mamba-3's complex SSM is equivalent to a real SSM with _____ rotations.

**A:** Data-dependent

---

## Card 34

**Q:** What is the 'reversal curse' in the context of large language models?

**A:** A basic generalisation failure where Transformers struggle to learn bidirectional relationships.

---

## Card 35

**Q:** What is 'State-Passing' (SP) in the context of hybrid Mamba-Transformer models?

**A:** An intervention used during post-training to enable better length extrapolation in long-context tasks.

---

## Card 36

**Q:** How does EFLA derive its exact solution for input injection?

**A:** By integrating the matrix exponential transition operator over the time step.

---

## Card 37

**Q:** What is the 'vanishing update' problem in EFLA?

**A:** The dampening of parameter updates for high-confidence features where the key norm is large.

---

## Card 38

**Q:** In Mamba-3, what does a higher MIMO rank $r$ do to the decode kernel performance?

**A:** It increases decoding FLOPs with minimal increase in wall-clock latency.

---

## Card 39

**Q:** What is the intended purpose of the $B$ and $C$ bias parameterization in Mamba-3?

**A:** To enable the model to capture Linear Time-Invariant components and improve downstream performance.

---

## Card 40

**Q:** The EFLA update rule maintains _____ time complexity while capturing exact continuous-time dynamics.

**A:** Linear

---

## Card 41

**Q:** Which DSL was used to implement the optimized Mamba-3 decode kernels?

**A:** CuTe DSL.

---

## Card 42

**Q:** Why is the Mamba-3 MIMO model considered Pareto-optimal for state usage?

**A:** It provides superior performance across various state sizes compared to Mamba-2 and SISO Mamba-3.

---

## Card 43

**Q:** What property of $A_t$ in EFLA allows the Taylor series of the matrix exponential to collapse?

**A:** The rank-1 structure.

---

## Card 44

**Q:** What is 'stiff dynamics' in the context of ODEs and linear attention?

**A:** Dynamics where numerical solvers require extremely small steps to remain stable, often leading to instability in low-order methods like Euler.

---

## Card 45

**Q:** How does EFLA handle the 'interference over long contexts' seen in basic linear attention?

**A:** By introducing an exact saturation mechanism that effectively manages memory erasure.

---

## Card 46

**Q:** Mamba-3's training protocol used _____ tokens to ensure fair cross-model comparison.

**A:** 100B

---

## Card 47

**Q:** What is the 'selectivity' feature in State Space Models like Mamba?

**A:** Making the state transitions dependent on the current input token.

---

## Card 48

**Q:** In EFLA, what is the 'input forcing term' $b_t$ defined as?

**A:** $k_t v_t^T$.

---

## Card 49

**Q:** What is the 'WY representation' used for in EFLA chunkwise parallelism?

**A:** Representing products of Householder-like matrices for hardware-efficient parallel scans.

---

## Card 50

**Q:** How does Mamba-3 achieve gains in 'state-tracking' on synthetic tasks?

**A:** Through complex-valued state updates that allow for rotational dynamics.

---

## Card 51

**Q:** Term: Arithmetic Intensity

**A:** Definition: A measure of operations per byte of memory access; higher intensity helps overcome memory-bandwidth bottlenecks.

---

## Card 52

**Q:** In EFLA, what activation function is replaced to create the 'Loose $\beta$' variant?

**A:** The Sigmoid function is replaced with Softplus to relax the upper-bound on decay.

---

## Card 53

**Q:** What is the global error order for the trapezoidal rule under specific regularity conditions?

**A:** Second-order ($O(\Delta_t^2)$).

---

## Card 54

**Q:** Why is the MIMO formulation 'implementation-friendly'?

**A:** It relies on fast matrix-matrix multiplications.

---

## Card 55

**Q:** How does Mamba-3's prefill latency compare to Mamba-2?

**A:** They are nearly identical, confirming that Mamba-3's additions do not introduce significant forward-pass overhead.

---

## Card 56

**Q:** What is the 'Stability-Responsiveness Trade-off' mentioned in the EFLA paper?

**A:** The balance between being robust against gradient outliers (stability) and maintaining fast optimization (responsiveness).

---

## Card 57

**Q:** In the context of the delta rule, what does the learning rate $\beta_t$ represent?

**A:** The integration step size for the continuous-time dynamical system.

---

## Card 58

**Q:** What does the 'Aha!' moment derivation in EFLA prove about the matrix exponential $e^{-\beta_t A_t}$?

**A:** It can be simplified to $I - \frac{1 - e^{-\beta_t \lambda_t}}{\lambda_t} A_t$.

---

## Card 59

**Q:** What is the 'needle-in-a-haystack' task used for?

**A:** Evaluating a model's ability to retrieve a specific piece of information from a long context.

---

## Card 60

**Q:** Mamba-3's hybrid configuration interleaves self-attention blocks with Mamba-3 blocks in a repeated _____ pattern.

**A:** Interleaved

---

## Card 61

**Q:** EFLA consistently outperforms DeltaNet on LAMBADA, which evaluates final word prediction based on _____ context.

**A:** Broad

---

## Card 62

**Q:** What is the purpose of the 'expm1' function in EFLA's implementation?

**A:** To preserve numerical precision when computing $1 - e^{-x}$ for very small exponents.

---

## Card 63

**Q:** What is the relationship between Linear Transformers and Performer models?

**A:** Both approximate softmax attention by replacing the kernel with a feature map for linear scaling.

---

## Card 64

**Q:** In Mamba-3, the $BC$ bias parameters are trainable and _____ independent.

**A:** Data

---

## Card 65

**Q:** What is the 'vanishing update' problem in EFLA fine-grained optimization?

**A:** Exponential suppression of gradient signals for high-confidence features.

---

## Card 66

**Q:** Why is 'FlashDecoding' mentioned in the comparison of Mamba-3 efficiency?

**A:** It represents a production-grade Transformer inference optimization used as a baseline for speed comparisons.

---

## Card 67

**Q:** What is the 'Pareto-frontier' in the context of Mamba-3?

**A:** The optimal trade-off curve between model performance and inference compute/memory budget.

---

## Card 68

**Q:** The EFLA update rule contracts the component of memory state $S_{t-1}$ aligned with $k_t$ by a factor of _____.

**A:** $e^{-\beta_t \lambda_t}$

---

## Card 69

**Q:** What regularity condition is typically required for second-order convergence in trapezoidal schemes?

**A:** Lipschitz continuity.

---

## Card 70

**Q:** In Mamba-3, complex dynamics are implemented at only the computational cost of _____.

**A:** RoPE (Rotary Positional Embedding)

---

# Mamba Quiz

## Question 1
Mamba-3 introduces methodological improvements over Mamba-2. Which discretization scheme does Mamba-3 employ to improve recurrence expressivity and approximation quality?

- [x] Generalized Trapezoidal Discretization
- [ ] First-order Explicit Euler Discretization
- [ ] Fourth-order Runge-Kutta (RK-4)
- [ ] Bilinear Transform using Zero-Order Hold

**Hint:** Consider the mathematical rule that provides a second-order approximation of an integral by treating the area as a specific four-sided shape.

## Question 2
In the context of Mamba-3's complex-valued dynamics, how are these implemented to maintain computational efficiency during inference?

- [x] Through an equivalence to data-dependent Rotary Position Embeddings (RoPE)
- [ ] By using Fast Fourier Transforms (FFT) for state updates
- [ ] By doubling the state dimension $d_{state}$ to handle imaginary components
- [ ] Via a static sine-cosine positional encoding applied to the input

**Hint:** Think about a popular technique used in Transformers to inject positional information through rotations.

## Question 3
What is the primary practical motivation for the Multi-Input Multi-Output (MIMO) formulation in Mamba-3?

- [x] To increase arithmetic intensity and mitigate memory-bound decoding
- [ ] To reduce the number of parameters in the mixer layer
- [ ] To enable faster training convergence via global convolutions
- [ ] To decrease the size of the recurrent state $d_{state}$

**Hint:** Focus on how the model manages the trade-off between memory access and mathematical operations on modern hardware.

## Question 4
Error-Free Linear Attention (EFLA) addresses numerical instability by solving the underlying ODE exactly. Which mathematical property of the dynamics matrix $A_t = k_t k_t^\top$ enables this computation in linear time?

- [x] The rank-1 structure of the matrix
- [ ] The skew-symmetry of the key projections
- [ ] The sparsity of the associative memory state
- [ ] The orthogonality of the value matrix

**Hint:** Identify the property of an outer product of two vectors that simplifies complex matrix operations like exponentials.

## Question 5
According to the EFLA research, what acts as the 'dynamic gate' regulating the trade-off between memory forgetting and retention?

- [x] The squared norm of the key vector $||k_t||^2$
- [ ] The learning rate $\beta_t$ of the AdamW optimiser
- [ ] The softmax temperature applied to the queries
- [ ] The dropout probability $p$ applied to input tokens

**Hint:** Look for a value derived from the key signal that determines how 'stiff' the dynamics of the memory state are.

## Question 6
Experiments with Mamba-3 hybrid architectures (5:1 SSM to Attention ratio) showed significant improvements in which area compared to pure SSMs?

- [x] Real-world retrieval tasks (e.g., SWDE and FDA)
- [ ] Arithmetic operations on small integers
- [ ] Total number of parameters required for a fixed accuracy
- [ ] Hardware-level decode latency on NVIDIA H100 GPUs

**Hint:** Pure recurrent models often struggle with 'needle-in-a-haystack' scenarios compared to Transformers.

## Question 7
In Mamba-3, what role does the 'BC bias' play regarding the model's architecture?

- [x] It provides an implicit LTI component that makes the short convolution layer redundant
- [ ] It serves as a normalisation layer for the queries and keys
- [ ] It ensures that the eigenvalues of the transition matrix are exactly $0.5$
- [ ] It acts as a safety guardrail against universal jailbreak attacks

**Hint:** Think about how the authors justified removing a standard 1D-convolutional component found in Mamba-1 and Mamba-2.

## Question 8
Deployment analysis of Mamba-3 suggests that the 'tipping point' between memory-bound and compute-bound kernels for a MIMO rank $r$ on an NVIDIA H100 occurs when $r$ is approximately:

- [x] $18$ (for fp32 data)
- [ ] $4$ (the default rank used)
- [ ] $128$ (the standard state size)
- [ ] $2048$ (the model dimension)

**Hint:** This value relates to the hardware's specific ratio of TFLOPS to DRAM bandwidth.

## Question 9
As the key norm $||k_t||^2$ approaches zero, the EFLA update rule asymptotically recovers which classical model?

- [x] The Delta Rule (DeltaNet)
- [ ] Softmax Attention (Transformer)
- [ ] Mamba-2 (SISO)
- [ ] Linear Transformer with Feature Maps

**Hint:** Look for the first-order integration method that EFLA improves upon by providing an exact solution.

## Question 10
Which trade-off is most characteristic of switching from a SISO to a MIMO Mamba-3 model during training and inference?

- [x] MIMO improves modeling performance and decoding efficiency but increases training compute costs
- [ ] MIMO reduces memory usage in training but slows down token generation
- [ ] MIMO requires a much larger recurrent state to achieve the same accuracy as SISO
- [ ] MIMO is only compatible with fp32 precision, while SISO supports bfloat16

**Hint:** Recall the discussion on why SISO was used for the main Language Modeling results despite MIMO's Pareto-optimality.

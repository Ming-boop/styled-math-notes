---
marp: true
theme: gaia
paginate: true
style: |
    :root {
        --color-background: #fff;
        --color-foreground: #333;
        --color-highlight: #f96;
        --color-dimmed: #888;
        --font-main: 'Times New Roman', sans-serif;
    }
    /* force the browser to print (light) backgrounds */
    @media print {
        * {
            -webkit-print-color-adjust: exact !important;
            print-color-adjust: exact !important;
        }
    }
    /* container look */
    .block {
        margin: 1em 0;
        padding: 0.65em 1em;
        border-radius: 8px;
        box-shadow: 0 1px 0 rgba(0, 0, 0, 0.04);
        border-left: 6px solid #888;
    }
    /* specific types */
    .block.problem {
        border-left-color: #d75b4a;
        background: #fff6f4;
    }
    .block.example {
    border-left-color: #f0c542;
    background: #fff9e6;
    }
    .block.definition {
        border-left-color: #2b7a0b;
        background: #f7fff3;
    }
    .block.theorem {
        border-left-color: #0b63b8;
        background: #f5fbff;
    }
    .block.proof {
        border-left-color: #b86f0b;
        background: #fffaf0;
    }
    .block.followup {
        border-left-color: #5a2e8a;
        background: #f6f3fb;
    }
    /* small title label */
    .block .label {
        font-weight: 700;
        margin-bottom: 0.35em;
        display: inline-block;
        text-transform: uppercase;
        font-size: 1.05em;
        opacity: 0.95;
    }
    /* optional: muted statement/italic proof marker */
    .block.definition em {
        font-style: italic;
        color: #333;
    }
    section {
        font-family: var(--font-main);
    }
    section.gaia::before {
        content: 'Friday Morning Seminar — October 31, 2025';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128);
    }
    section.part1::before {
        content: 'Part 1: Preliminaries';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part2::before {
        content: 'Part 2: Lambda-Supermartingale and Invariance';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part3::before {
        content: 'Part 3: Synthesis of Martingales';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
math: mathjax

---

<!-- _class: lead gaia -->

# Controlled Supermartingale Functions for Stochastic Differential Equations: Inference and Applications

Masoumeh Ghanbarpour, Guillaume O. Berger, and Sriram Sankaranarayanan

[CDC2025 Paper](https://home.cs.colorado.edu/~srirams/papers/supermartingales-sde-cdc-2025.pdf)

---

<!-- _class: gaia -->

## Outline

* Preliminaries
* $\lambda$-Supermartingale and Invariance
* Synthesis of Martingales

---

<!-- _class: part1 -->

(Super)Martingale

$$
\mathbb{E}(|X_n|) < \infty, \mathbb{E}(X_{n+1} \mid X_1, \cdots, X_{n}) (\le)= X_{n}
$$

A continuous-time (super)martingale with respect to another sequence

$$
\mathbb{E}(|Y_t|) < \infty, \mathbb{E}(Y_t \mid \{X_\tau, \tau \le s \}) (\le)= Y_{s}, \quad \forall s \le t
$$

Consider stochastic differential equation (SDE) as

$$
\mathrm{d} x = f(x) \mathrm{d}t + \sigma(x) \mathrm{d}W \tag{3}
$$

where $x \in \mathbb{R}^n$ is the state of system in $\mathbb{R}^n$, $W$ is $p$-dimensional Brownian motion, $f: \mathbb{R}^n \to \mathbb{R}^n$ is the drift term, and $\sigma: \mathbb{R}^n \to \mathbb{R}^{n \times p}$ is the diffusion term.

---

<!-- _class: part1 -->

<div class="block definition">
<div class="label"> definition 5 & 6 </div>

(<strong>Semigroup of Operators</strong>): A one-parameter family of linear operators ${P_t}$ which satisfies the following properties is called _semigroup_: $P_0 = \mathsf{Id}$, $P_{t+s}= P_t \circ P_s, \forall s, t \ge 0$.
(<strong>Koopman Operator</strong>): For every $t \in I, P_t: \mathcal{B}(E) \to \mathcal{B}(E)$ is a positive linear operator with respect to the Markov process $\{X_t \}_{t \in I}$. Let $h \in \mathcal{B}(E)$ then
$$
P_t h(x) = \mathbb{E}^x [h(X_t)] \tag{4}
$$
$\{P_t \}_{t \in I}$ has semigroup property(see Chapman-Kolmogorov equation).
</div>

---

<!-- _class: part1 -->

<div class="block definition">
<div class="label"> definition 7 </div>

(<strong>Generator of Koopman Operator</strong>) The operator $\mathcal{L}: \mathcal{D_L} \to \mathcal{B}(E)$ is called the (infinitesimal) generator of the semigroup $P_t$, and is defined as

$$
\mathcal{L}(h(x)) = \lim\limits_{t \to 0}  \dfrac{P_t h(x) - h(x)}{t}, \quad x \in E \tag{5}
$$

where $\mathcal{D_L}$ be the set of all $f \in \mathcal{B}(E)$ that the the limit exists.
</div>

$$
\mathcal{L}(h(x)) = \nabla h(x)^\mathsf{T} f(x) +\dfrac{1}{2} \text{tr} \left(\left(\sigma(x) \sigma(x)^\mathsf{T} \right) \times \nabla^2 h(x) \right) \tag{7}
$$

---

<!-- _class: part1 -->

Consider an SDE system where the drift term includes an additional component influenced by a control input:

$$
\mathrm{d} x = (f(x) + g(x) u) \mathrm{d}t + \sigma(x) \mathrm{d}W \tag{8}
$$

where $g: \mathbb{R}^n \to \mathbb{R}^{n \times m}$ and the control input $u \in \mathbb{R}^m$.

Let $\mathcal{L}_u$ be the generator of $(8)$, and $\mathcal{L}$ be the generator of the SDE without the term $g(x) u$ involving the control input. Then, we can express $\mathcal{L}_u$ in terms of $\mathcal{L}$ of SDE without control term, along with an additional term that captures the influence of the control input, as

$$
\mathcal{L}_u h(x) = \mathcal{L} h(x) + \nabla h(x)^\mathsf{T} g(x) u \tag{9}
$$

---

<!-- _class: part1 -->

### Problem Statement

**Inputs**: We are given a controlled SDE model according to $(8)$ defined by (control affine) functions $f(x)$, $g(x)$ and $\sigma(x)$, a set of initial states $\mathcal{X}_0$, unsafe states $\mathcal{X}_u$ ($\mathcal{X}_0 \cap \mathcal{X}_u = \emptyset$), time horizon $T > 0$ and probablity threshold $\rho \in (0, 1)$.

**Output**: A continuous and differentiable control feedback law $u = \kappa(x)$ such that for all initial conditions $x_0 \in \mathcal{X}_0$, the probability that a trajectory of the resulting closed loop system starting from $x_0$ at time $t = 0$ reaches a state $x_t \in \mathcal{X}_u$ for $t \in [0, T]$ is less than or equal to $\rho$.

$f(x)$, $g(x)$ and $\sigma(x)$ are multivariate polynomials over $x$.

---

<!-- _class: part2 -->

<div class="block definition">
<div class="label"> definition 8 & 9 </div>

(<strong>Supermartingale Functions</strong>) An observable function$h: E \to \mathbb{R}$ is a supermartingale for stochastic process $\{X_t \}$ iff $\{h(X_t) \}$ is a supermartingale process.

(<strong>$\lambda$-supermartingale</strong>) An observable $h: E \to \mathbb{R}$ is called $\lambda$-supermartingale, if there exists a  continuous function $\lambda: I \to \mathbb{R}$ such that $e^{\int_0^t \lambda(s) \mathrm{d} s} h$ is  a supermartingale function.
</div>

A sufficient condition: $h \in \mathcal{D_L}$ is $\lambda$-supermartingale with a continuous and differentiable function $\lambda: I \to \mathbb{R}$ if $\mathcal{L}h \le λh$($\mathcal{L}h \le 0$ for supermartingale).

---

<!-- _class: part2 -->

### Probabilistic Forward Invariance

$\tau_D(\omega)$: first hitting time of process $\{X_t \}_{t \in I}$ to the set $D$.
$O_c$: super-level set of $h$ for $c > 0$.
$\mathbb{P}^x \left( \tau_{O_c} \le T \right) = \mathbb{P}^x \left( \sup\limits_{t \in [0, T]} h(x) \ge c \right)$

---

<!-- _class: part2 -->

<div class="block theorem">
<div class="label"> lemma 4 </div>

Given the SDE system in $(3)$, and the corresponding infinitesimal generator $\mathcal{L}$. Let $h \in \mathcal{D_L}$ be a nonnegative $\lambda$-supermartingale function with the continuous function $\lambda: \mathbb{R}_{\ge 0} to \mathbb{R}$. Let $S \subset \mathbb{R}^n$ be such that $S \cap O_c = \emptyset$, for $x \in S$,
$$
\mathbb{P}^x \left( \tau_{O_c} \le T \right) \le \dfrac{1}{c} \left( \sup\limits_{t \in [0, T]} e^{\int_0^t \lambda(s) \mathrm{d} s} \right) h(x) \tag{14}
$$
</div>

---

<!-- _class: part2 -->

<div class="block theorem">
<div class="label"> lemma 5 </div>

Given the controlled affine SDE in $(8)$, let $h$ be a nonnegative $\lambda$-supermartingale function with the continuous function $\lambda$. Suppose $S \subset \mathbb{R}^n$ and $h(x) \le c$ for $x \in S$. If there exists a differentiable control law $u = \kappa(x)$ such that for each $x \in S: \mathcal{L}_u(h(x)) = \mathcal{L}(h(x)) + \nabla h(x)^\mathsf{T} g(x) u(x) \le \lambda h(x)$, then the probability of reaching $O_c$ at time $t$ starting from $x \in S$ is bounded as:
$$
\mathbb{P} (h(X_t) ≥ c) \le \dfrac{1}{c} e^{\int_0^t \lambda(s) \mathrm{d} s} \mathbb{E}[h(X_0)]
$$
</div>

---

<!-- _class: part2 -->

### Synthesis criteria

$$
h(x) \ge 0, \forall x \in \mathcal{X}; \quad \lambda(t) \le 0, \forall t \in [0, T]
$$
$$
h(x) \le \rho, \forall x \in \mathcal{X}_0; \quad h(x) \ge 1, \forall x \in \mathcal{X_u}
$$
$$
\mathcal{L}_u(h(x)) = \mathcal{L}(h(x)) + \nabla h(x)^\mathsf{T} g(x) \kappa(x) \le \lambda(t) h(x), \forall x \in \mathcal{X}, t \in [0, T]
$$

<div class="block theorem">
<div class="label"> theorem 2 </div>

Given $f, g, \sigma, \mathcal{X}_0, \mathcal{X_u}$, time horizon $T > 0$ and probability threshold $\rho \in (0, 1)$, the controlled SDE $(8)$ under the feedback $u = \kappa(x)$ satisfies $\mathbb{P} (\tau_{\mathcal{X}_u} \le T) \le \rho$.
</div>

---

<!-- _class: part3 -->

### Polynomial SDEs without Control

Our goal is to synthesize a $\lambda$-supermartingale function $h(x) \in \mathbb{R}[x]$ and $\lambda(t) \in \mathbb{R}[t]$ that satisfies the condition:

$$
\forall x \in \mathcal{X} \subset \mathbb{R}^n, \mathcal{L}(h(x)) \le \lambda h(x) \tag{19}
$$

$d, q$: fixed degree bound for $h(x)$ and $\lambda(t)$;
$h(x) = v_h^\mathsf{T} \phi_d(x)$, $v_h \in \mathbb{R}^{r_d}$, $\lambda(t) = \omega_\lambda^\mathsf{T} \psi_q(t)$, $\omega_\lambda \in \mathbb{R}^{r_q}$.
As $\mathcal{L}$ is a linear operator, we have $\mathcal{L}(h(x)) = \mathcal{L}(v_h^\mathsf{T} \phi_d(x)) = v_h^\mathsf{T} \mathcal{L}(\phi_d(x))$.

$$
\begin{align}
    & \text{find} \ v_h, \omega_\lambda \\
    \text{s.t.} \quad & \left(v_h^\mathsf{T} \phi_d(x) \right) \times \left(\omega_\lambda^\mathsf{T} \psi_q(t) \right) - \left(v_h^\mathsf{T} \mathcal{L} \phi_d(x) \right) \ \text{is SOS}
\end{align} \tag{20}
$$

---

<!-- _class: part3 -->

For simplicity, we write $v = v_h$ and $\omega = \omega_\lambda$.
Let $h_0 = v_0^\mathsf{T} \phi_d(x)$ be an initial guess. The proposed iterative scheme solves a the following optimization problem to find $h_{i+1} = v_{i + 1}^\mathsf{T} \phi_d(x)$ given $h_i = v_i^\mathsf{T} \phi_d(x)$ for $i \ge 0$:

$$
\begin{align}
    & \min\limits_{v, \omega} \|v - v_i\|_2 \\
    \text{s.t.} \quad & \left(v_i^\mathsf{T} \phi_d(x) \right) \times \left(\omega^\mathsf{T} \psi_q(t) \right) - \left(v^\mathsf{T} \mathcal{L} \phi_d(x) \right) \ \text{is SOS} \\
    & \text{Addtional constraints over $v, \omega$}
\end{align} \tag{21}
$$

$(21)$ computes $(v_{i+1}, \omega_{i+1})$ as a function of $v_i$. Note that $\omega_i$ is not used.

---

<!-- _class: part3 -->

### Controlled Supermartingale Synthesis

$$
\mathcal{L}_u(h(x)) \le \lambda h(x) \rightarrow \left(v_i^\mathsf{T} \phi_d(x) \right) \times \left(\omega^\mathsf{T} \psi_q(t) \right) - \left(v^\mathsf{T} \mathcal{L} \phi_d(x) \right) - \nabla h(x)^\mathsf{T} g(x) u \ge 0
$$

Parameterize $u = K \phi_d(x), K \in \mathbb{R}^{m \times r_d}$:

$$
\begin{align}
    & \min\limits_{v, \omega, K} \|v - v_i\| + \|K - K_i\| \\
    \text{s.t.} \quad & \left(v_i^\mathsf{T} \phi_d(x) \right) \times \left(\omega^\mathsf{T} \psi_q(t) \right) - \left(v^\mathsf{T} \mathcal{L} \phi_d(x) \right) \\
    & + \left(v_i^\mathsf{T} \nabla \phi_d(x) \right) \times g(x) \times\left(K \phi_d(x) \right) \ \text{is SOS} \\
    & \text{Addtional constraints over $v, \omega, K$}
\end{align} \tag{22}
$$

---

<!-- _class: part3 -->

$$
\begin{align}
    & \min\limits_{v, \omega, K} \|v - v_i\| + \|K - K_i\| \quad \text{s.t.}\\
    \text{C1.} & \quad v_i^\mathsf{T} \phi_d(x) \ \text{is SOS}, \ x \in \mathcal{X} \\
    \text{C2.} & \quad - \omega^\mathsf{T} \psi_q(t) \ \text{is SOS}, \ t \in [0, T] \\
    \text{C3.} & \quad v_i^\mathsf{T} \phi_d(x) \le \rho, \ x \in \mathcal{X}_0 \\
    \text{C4.} & \quad v_i^\mathsf{T} \phi_d(x) \ge 1, \ x \in \mathcal{X}_u \\
    \text{C5.} & \quad \left(v_i^\mathsf{T} \phi_d(x) \right) \times \left(\omega^\mathsf{T} \psi_q(t) \right) - \left(v^\mathsf{T} \mathcal{L} \phi_d(x) \right) \\
    & \quad + \left(v_i^\mathsf{T} \nabla \phi_d(x) \right) \times g(x) \times\left(K \phi_d(x) \right) \ \text{is SOS}, \\
    & \qquad \qquad(t, x) \in [0, T] \times \mathcal{X} \\
\end{align} \tag{22}
$$

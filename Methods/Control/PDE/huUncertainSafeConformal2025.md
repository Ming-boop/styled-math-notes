# From Uncertain to Safe: Conformal Adaptation of Diffusion Models  for Safe PDE Control

## Synthesis

### Contribution

### Related Work

## Preliminary

### Problem Setup

Consider the following safe control problem of PDE constrained systems:

<div class="block problem">
<div class="label"> problem </div>

$$
\begin{equation}
    \begin{align*}
        & \textbf{w}^* = \argmin\limits_{\textbf{w}} \mathcal{J}(\textbf{u},\textbf{w})\\
        \text{s.t.} \quad & \mathcal{C}(\textbf{u},\textbf{w}) = 0 & \text{(Boundary condition)}\\
        & s(\textbf{u}) \le s_0 & \text{(Safety constraint)}
    \end{align*}
\end{equation}
$$
</div>

where $\textbf{u}(t,\textbf{x}): [0,T] \times \Omega \to \mathbb{R}^{d_\textbf{u}}$ is the system’s state trajectory with dimension $d_\textbf{u}$ and $\textbf{w}(t,\textbf{x}): [0,T] \times \Omega \to \mathbb{R}^{d_\textbf{w}}$ is the external control signal with dimension $d_\textbf{w}$. They are both defined on the time range $[0,T] \subset \mathbb{R}$ and spatial domain $\Omega \subset \mathbb{R}^D$. $\mathcal{J}(\textbf{u},\textbf{w})$ is the objective of the control problem.

### Diffusion Models and Diffusion Control

The diffusion process $q(\textbf{x}^{k+1} \mid \textbf{x}^{k}) = \mathcal{N}(\textbf{x}^{k+1}; \sqrt{\alpha_k} \textbf{x}^{k}, (1-\alpha_k) \textbf{I})$ corrupts the data distribution $p(\textbf{x}^{0})$ to a prior distribution $\mathcal{N}(0, \textbf{I})$, and the denoising process $p_\theta(\textbf{x}^{k-1} \mid \textbf{x}^{k}) =  \mathcal{N}(\textbf{x}^{k-1}; \mu_\theta(\textbf{x}^{k}, k), \sigma_k \textbf{I})$ makes sampling in a reverse direction. $\{\alpha_k\}_{k=1}^K$ and $\{\sigma_k\}_{k=1}^K$ are the noise and variance schedules.

<div class="block followup">
<div class="label"> followup </div>

$q(\textbf{x}^{k+1} \mid \textbf{x}^{k})$ is a <strong>conditional probability density</strong> (kernel) that gives the probability density of $\textbf{x}^{k+1}$ given $\textbf{x}^{k}$. The right side of the formula is the <strong>forward</strong> step, meaning that given $\textbf{x}^{k}$,
$$
\textbf{x}^{k+1} \sim \mathcal{N}(\sqrt{\alpha_k} \textbf{x}^{k}, (1-\alpha_k) \textbf{I})
$$
or
$$
\textbf{x}^{k+1} = \sqrt{\alpha_k} \textbf{x}^{k} + (1-\alpha_k) \epsilon, \quad \epsilon \sim \mathcal{N}(0, \textbf{I})
$$
$p_\theta(\textbf{x}^{k-1} \mid \textbf{x}^{k})$ is a <strong>parametric model</strong> for the <strong>reverse</strong> step.
</div>

<div class="block followup">
<div class="label"> followup </div>

In standard [DDPM](https://hojonathanho.github.io/diffusion/), a closed-form expression for $\textbf{x}^{k}$ in terms of $\textbf{x}^{0}$ is given by
$$
\textbf{x}^{k} = \sqrt{\bar\alpha_k} \textbf{x}^{0} + \sqrt{1-\bar\alpha_k} \epsilon, \quad \epsilon \sim \mathcal{N}(0, \textbf{I})
$$
where $\bar\alpha_k = \prod\limits_{i=1}^k \alpha_i$.

So the exact posterior distribution $q(\textbf{x}^{k-1} \mid \textbf{x}^{k}, \textbf{x}^{0})$ is Gaussian.

By definition of conditional probability:
$$
\begin{split}
    q(\textbf{x}^{k-1} \mid \textbf{x}^{k}, \textbf{x}^{0}) & = \dfrac{q(\textbf{x}^{k-1}, \textbf{x}^{k} \mid \textbf{x}^{0})}{q(\textbf{x}^{k} \mid \textbf{x}^{0})}\\
    & = \dfrac{q(\textbf{x}^{k} \mid \textbf{x}^{k-1}, \textbf{x}^{0})q(\textbf{x}^{k-1} \mid\textbf{x}^{0})}{q(\textbf{x}^{k} \mid \textbf{x}^{0})}\\
    & = \dfrac{q(\textbf{x}^{k} \mid \textbf{x}^{k-1})q(\textbf{x}^{k-1} \mid\textbf{x}^{0})}{q(\textbf{x}^{k} \mid \textbf{x}^{0})} \quad \text{(Markov property)}\\
    & \propto q(\textbf{x}^{k} \mid \textbf{x}^{k-1})q(\textbf{x}^{k-1} \mid\textbf{x}^{0})
\end{split}
$$
</div>

# From Uncertain to Safe: Conformal Adaptation of Diffusion Models  for Safe PDE Control

## Synthesis

### Contribution

### Related Work

## Preliminaries

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

In standard [DDPM](https://hojonathanho.github.io/diffusion/), a closed-form expression for $\textbf{x}^{k}$ in terms of $\textbf{x}^{0}$ is given by
$$
\textbf{x}^{k} = \sqrt{\bar\alpha_k} \textbf{x}^{0} + \sqrt{1-\bar\alpha_k} \epsilon, \quad \epsilon \sim \mathcal{N}(0, \textbf{I})
$$
where $\bar\alpha_k = \prod\limits_{i=1}^k \alpha_i$.

So the exact posterior distribution $q(\textbf{x}^{k-1} \mid \textbf{x}^{k}, \textbf{x}^{0})$ is Gaussian.
</div>

<div class="block proof">
<div class="label"> proof </div>

By definition of conditional probability:
$$
\begin{split}
    q(\textbf{x}^{k-1} \mid \textbf{x}^{k}, \textbf{x}^{0}) & = \dfrac{q(\textbf{x}^{k-1}, \textbf{x}^{k} \mid \textbf{x}^{0})}{q(\textbf{x}^{k} \mid \textbf{x}^{0})}\\
    & = \dfrac{q(\textbf{x}^{k} \mid \textbf{x}^{k-1}, \textbf{x}^{0}) q(\textbf{x}^{k-1} \mid\textbf{x}^{0})}{q(\textbf{x}^{k} \mid \textbf{x}^{0})}\\
    & = \dfrac{q(\textbf{x}^{k} \mid \textbf{x}^{k-1}) q(\textbf{x}^{k-1} \mid\textbf{x}^{0})}{q(\textbf{x}^{k} \mid \textbf{x}^{0})} \quad \text{(Markov property)}\\
    & \propto q(\textbf{x}^{k} \mid \textbf{x}^{k-1}) q(\textbf{x}^{k-1} \mid\textbf{x}^{0})
\end{split}
$$
Let $\beta_k = 1-\alpha_k$, the two Gaussian factors are
$$
q(\textbf{x}^{k} \mid \textbf{x}^{k-1}) = (2\pi\beta_k)^{-\frac{d}{2}} \exp \left(-\dfrac{1}{2\beta_k} \lVert \textbf{x}^{k} - \sqrt{\alpha_k} \textbf{x}^{k-1} \rVert^2 \right)\\
q(\textbf{x}^{k-1} \mid\textbf{x}^{0}) = (2\pi(1-\bar\alpha_{k-1}))^{-\frac{d}{2}} \exp\left(-\dfrac{1}{2(1-\bar\alpha_{k-1})} \lVert \textbf{x}^{k-1} - \sqrt{\bar\alpha_{k-1}} \textbf{x}^{0} \rVert^2 \right)
$$
so
$$
\begin{split}
    q(\textbf{x}^{k-1} \mid \textbf{x}^{k}, \textbf{x}^{0}) & \propto C_0 \exp \left[-\dfrac{1}{2} \left(\dfrac{1}{\beta_k} \lVert \textbf{x}^{k} - \sqrt{\alpha_k} \textbf{x}^{k-1} \rVert^2 + \dfrac{1}{1-\bar\alpha_{k-1}} \lVert \textbf{x}^{k-1} - \sqrt{\bar\alpha_{k-1}} \textbf{x}^{0} \rVert^2 \right)\right]\\
    & = C_1 \exp \left[-\dfrac{1}{2} \left(\left(\dfrac{\alpha_k}{\beta_k} + \dfrac{1}{1-\bar\alpha_{k-1}} \right) \lVert \textbf{x}^{k-1} \rVert^2 - 2 \left( \dfrac{\sqrt{\alpha_k}}{\beta_k}\textbf{x}^{k} + \dfrac{\sqrt{\bar\alpha_{k-1}}}{1-\bar\alpha_{k-1}}\textbf{x}^{0} \right) \textbf{x}^{k-1} \right) \right]
\end{split}
$$
The covariance is given by
$$
\Sigma = \left( \dfrac{\alpha_k}{\beta_k} + \dfrac{1}{1-\bar\alpha_{k-1}} \right)^{-1} = \left( \dfrac{1-\bar\alpha_{k}}{(1-\alpha_k)(1-\bar\alpha_{k-1})} \right)^{-1} = \dfrac{\beta_k (1-\bar\alpha_{k-1})}{1-\bar\alpha_{k}}
$$
The mean is given by
$$
\mu = \Sigma \left( \dfrac{\sqrt{\alpha_k}}{\beta_k}\textbf{x}^{k} + \dfrac{\sqrt{\bar\alpha_{k-1}}}{1-\bar\alpha_{k-1}}\textbf{x}^{0} \right) = \dfrac{(1-\bar\alpha_{k-1}) \sqrt{\alpha_k}}{1-\bar\alpha_{k}}\textbf{x}^{k} + \dfrac{\beta_k \sqrt{\bar\alpha_{k-1}}}{1-\bar\alpha_{k}} \textbf{x}^{0} = \mu(\textbf{x}^{k}, \textbf{x}^{0})
$$
</div>

<div class="block followup">
<div class="label"> followup </div>

How the model $p_\theta$ is implemented in practice (two common parameterizations):

#### *Mean-direct parameterization*

The network directly outputs $\mu_\theta(\textbf{x}^{k}, k)$, (and possibly $\sigma_k$). Sampling uses the Gaussian above.

#### *Epsilon-prediction parameterization (very common)*

Instead of predicting $\mu$ directly, the neural network predicts the noise $\epsilon_\theta(\textbf{x}^{k}, k)$ that was added in the forward process.

Because we have the closed form $\textbf{x}^{k} = \sqrt{\bar\alpha_k} \textbf{x}^{0} + \sqrt{1-\bar\alpha_k} \epsilon$ we can estimate $\textbf{x}^{0}$ from a noisy $\textbf{x}^{k}$ and a noise prediction:
$$
\hat{\textbf{x}}^{0} = \dfrac{\textbf{x}^{k}-\sqrt{1-\bar\alpha_k} \epsilon_\theta(\textbf{x}^{k}, k)}{\sqrt{\bar\alpha_k}}
$$
Plugging this $\hat{\textbf{x}}^{0}$ into the closed-form posterior mean $\mu(\textbf{x}^{k}, \textbf{x}^{0})$ yields the practical sampling mean used by many implementations.
$$
\mu_\theta(\textbf{x}^{k}, k) = \dfrac{1}{\sqrt{\alpha_k}} \left[ \textbf{x}^{k} - \dfrac{\beta_k}{\sqrt{1-\bar\alpha_k}} \epsilon_\theta(\textbf{x}^{k}, k) \right]
$$
A simple but popular training loss is
$$
\mathcal{L} = \mathbb{E}_{\textbf{x}^{0}, \epsilon, k} \left[ \lVert \epsilon - \epsilon_\theta(\textbf{x}^{k}, k) \rVert^2 \right]
$$
</div>

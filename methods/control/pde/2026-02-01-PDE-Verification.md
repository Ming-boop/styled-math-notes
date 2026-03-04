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
        content: 'Group Seminar — February 1, 2026';
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
        content: 'Part 2: Barrier Certificate for ODE Systems';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part3::before {
        content: 'Part 3: Barrier Certificate for PDE Systems';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part4::before {
        content: 'Part 4: Statistical Model Checking for PDE Systems';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
math: mathjax

---

<!-- _class: lead gaia -->

# Verification of Dynamical Systems Governed by Partial Differential Equations

Februry 2, 2026

---

<!-- _class: gaia -->

## Outline

* Preliminaries
* Barrier Certificate for ODE Systems
* Barrier Certificate for PDE Systems
* Statistical Model Checking for PDE Systems

$$
\def\R{\mathbb{R}}
\def\S{\mathcal{S}}
\def\U{\mathcal{U}}
\def\tran{\mathsf{T}}
\def\dd{\mathrm{d}}
\def\EE{\mathbb{E}}
\def\ind{\mathbf{1}}
$$

---

<!-- _class: part1 -->

<div class="block definition">
<div class="label"> definition </div>

<strong>Continuous System Model</strong>

The system dynamics are given by the ordinary differential equation

$$ \dot{x}(t) = f(x(t), u(t), t), \quad x(0) \in X_0 $$

where $x \in X, X \subset \R^n$ is the state space; $u \in U, U \subset \R^m$ is the input space (optional); $f: X \times U \to \R^n$ is a **locally Lipschitz** continuous vector field; $X_0 \subset X$ is the set of initial states.

</div>

---

<!-- _class: part1 -->

<div class="block definition">
<div class="label"> definition </div>

<strong>Trajectory (Semantics)</strong>

For an input function $u: \R_{\ge 0} \to U$, a trajectory is a function $x: \R_{\ge 0} \to X$ such that $x(0) \in X_0$ and $\dot{x}(t) = f(x(t), u(t), t)$. Let $\mathcal{S}$ denote the set of all such trajectories.

<strong>Specification</strong>

A specification is a logical formula $\varphi$ interpreted over trajectories.
State predicate: $P: X \to \set{\top, \bot}$; temporal logic.

</div>

---

<!-- _class: part1 -->

<div class="block definition">
<div class="label"> definition </div>

<strong>Verification Problem</strong>

A trajectory $x(t)$ satisfies specification $\varphi$, written $x \models \varphi$.

Example (safety) $x \models \square P \Leftrightarrow \forall t \ge 0, P(x(t)) = \top$.

The verification problem for a continuous system is to decide whether $\forall x \in \S, x \models \varphi$, i.e., do all trajectories starting from $X_0$, under all admissible inputs, satisfy the specification $\varphi$?

</div>

---

<!-- _class: part2 -->

<div class="block problem">
<div class="label"> problem setup </div>

Consider a polynomial system $\dot{x} = f(x)$ with initial set $X_0 = \set{x \mid g_i(x) \ge 0, i = 1, \cdots, m}$ and unsafe set $X_u = \set{x \mid h_i(x) \ge 0, i = 1, \cdots, n}$ where $g_i, h_i$ are polynomials.

We seek a polynomial barrier certificate $B(x)$ such that

$$
\begin{aligned}
    & B(x) \le 0, \ \forall x \in X_0; \quad B(x) > 0, \ x \in X_u \\
    & \dot{B}(x) = \dfrac{\partial B}{\partial x} \dot{x} \le 0, \ \forall x \in X (\text{convex}) \\
    & \text{or} \quad \dot{B}(x) = \dfrac{\partial B}{\partial x} \dot{x} < 0, \ \text{s.t.} \ B(x) = 0 (\text{nonconvex})
\end{aligned}
$$

</div>

---

<!-- _class: part2 -->

<div class="block theorem">
<div class="label"> sos solution </div>

<strong>SOS from</strong> $p(x)=\sum_{i=1}^{k}g_{i}(x)^{2}$.

Decision variables: polynomial $B(X)$, SOS multipliers $\sigma_i(x), \rho_i(x), \lambda(x)$

$$
\begin{aligned}
    & - B(x) - \sum_i \sigma_i(x) g_i(x) \in \text{SOS} \\
    & B(x) - \epsilon - \sum_i \sigma_i(x) h_i(x) \in \text{SOS} \\
    & - \dfrac{\partial B}{\partial x} \dot{x} - \lambda(x) B(x) \in \text{SOS}
\end{aligned}
$$

</div>

---

<!-- _class: part3 -->

<div class="block problem">
<div class="label"> problem setup </div>

$$
\begin{equation}
    \begin{cases}
        \dfrac{\partial u(x, t)}{\partial t} = \mathscr{F} u(x, t), \quad x \in \Omega, t \in [0, T] \\
        y(t) = \mathscr{H} u(x, t) \\
        u(x, 0) = u_0(x)
    \end{cases}
\end{equation}
$$

where $u \in \U, u_0 \in \U_0 \subset \U$ and $\mathscr{F}, \mathscr{H}: \U \to \R$. The unsafe set is defined as $y_u = \set{u: \mathscr{H}u \le 0}$.

</div>

---

<!-- _class: part3 -->

<div class="block theorem">
<div class="label"> barrier functional </div>

If there exists a barrier functional $B(u(x, t))$ such that

$$
\begin{equation}
    \begin{aligned}
        & B(u(x, t)) - B(u_0(x)) > 0, \quad \forall u \in y_u, u_0 \in \U_0 \\
        & \dfrac{\dd B(u(x, t))}{\dd t} \le 0, \quad \forall u \in \U
    \end{aligned}
\end{equation}
$$

</div>

---

<!-- _class: part3 -->

<div class="block theorem">
<div class="label"> work with integrals </div>

The author try to solve the class of problems

$$
\begin{equation}
    \begin{aligned}
        & \int_0^1 f(x, t, D^\alpha u) \dd x \ge 0 \\
        \text{s.t.} \quad & \int_0^1 s_i(x, t, D^\alpha u) \dd x \ge 0, \ i = 1, \cdots, r
    \end{aligned}
\end{equation}
$$

For example
$$
B(x, t) = \int_\Omega \left[ \eta^{\lceil \frac{k}{2} \rceil} (D^\alpha u) \right]^\tran \bar{B}(\theta, t) \left[ \eta^{\lceil \frac{k}{2} \rceil} (D^\alpha u) \right] \dd \theta
$$

</div>

---

<!-- _class: part4 -->

<div class="block problem">
<div class="label"> problem setup </div>

$$
\begin{equation}
    \begin{cases}
        \dfrac{\partial u(x, t)}{\partial t} = Au(x, t) - V(x, t) u(x, t) + f(x, t) \\
        u(x, t) = u_b(x, t), \quad \forall x \in \partial \Omega, t \in [0, T] \\
        u(x, 0) = u_0(x), \quad \forall x \in \Omega
    \end{cases}
\end{equation}
$$

where $\Omega \subset \R^n$, $\mu, \sigma, V, f, u_b, u_0$ are known functions and $u : \Omega \times [0, T] \to \R$ is the unknown function.

$$
\begin{equation}
    Au = \sum_{i=1}^n \mu_{i}(x, t) \dfrac{\partial u}{\partial x_i} + \dfrac{1}{2} \sum_{i=1}^n \sum_{j=1}^n \left( \sigma(x, t) \sigma(x, t) ^\tran\right)_{ij} \dfrac{\partial^2 u}{\partial x_i \partial x_j}
\end{equation}
$$

</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> time reversal </div>

Define the backward function $v(x, s) = u(x, t - s)$ for $s \in [0, t]$, then the backward equations are obtained

$$
\begin{equation}
    \begin{cases}
        \dfrac{\partial v(x, s)}{\partial s} + \tilde{A} v(x, s) - \tilde{V}(x, s) v(x, s) + \tilde{f}(x, s) = 0 \\
        v(x, s) = u_b(x, t - s), \quad \forall x \in \partial \Omega, s \in [0, t] \\
        v(x, t) = u_0(x), \quad \forall x \in \Omega
    \end{cases}
\end{equation}
$$

where $\tilde{V}(x, s) = V(x, t - s), \tilde{f}(x, s) = f(x, t - s)$ and the infinitesimal generator $\tilde{A}$  with $\tilde{\mu}_{i}(x, s) = \mu_i(x, t - s), \tilde{\sigma}(x, s) = \sigma(x, t - s)$.

</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> transform to sde </div>

Let the stochastic process $X_s$ solve the stochastic differential equation

$$
\begin{equation}
    \dd X_s = \tilde\mu(X_s, s) \dd s + \tilde\sigma(X_t, s) \dd W_s, \quad X_0 = x \in \Omega
\end{equation}
$$

where $W_s$ is standard Brown motion. Define the *first exit time* from the domain $\Omega$

$$
\begin{equation}
    \tau := \inf \set{s \ge 0 : X_s \notin \Omega}
\end{equation}
$$

</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> expectation result </div>

$$
\begin{equation}
    u(x, t) = \EE_x \left[ g(\tau \land t) v(X_{\tau \land t}, \tau \land t) + \int_0^{\tau \land t} g(\theta) \tilde{f}(X_\theta, \theta) \dd \theta \right]
\end{equation}
$$

with discount factor $g(r) = e^{-\int_0^r \tilde{V}(X_\theta, \theta) \dd \theta}$.

</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> deal with boundaries </div>

If $\tau \le t$ then at $r = \tau$ we hit the boundary

$$
\begin{equation}
    v(X_{\tau \land t}, \tau \land t) = v(X_\tau, \tau) = u_b(X_\tau, t - \tau)
\end{equation}
$$

If $\tau > t$ then at $r = t$ we hit the initial condition

$$
\begin{equation}
    v(X_{\tau \land t}, \tau \land t) = v(X_t, t) = u_0(X_t)
\end{equation}
$$

</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> final result </div>

Finally substitute back $\tilde{V}(x, s) = V(x, t - s), \tilde{f}(x, s) = f(x, t - s)$ and split the value according to whether the path exited

$$
\begin{equation}
    u(x, t) = \
    \begin{aligned}[t]
        \EE_x \Big[ \ind_{\set{\tau > t}} & \cdot e^{-\int_0^t V(X_\theta, t - \theta) \dd \theta} u_0(X_t) \\
        & + \ind_{\set{\tau \le t}} \cdot e^{-\int_0^\tau V(X_\theta, t - \theta) \dd \theta} u_b(X_\tau, t - \tau) \\
        & + \int_0^{\tau \land t} e^{-\int_0^s V(X_\theta, t - \theta) \dd \theta} f(X_s, t - s) \dd s \Big]
    \end{aligned}
\end{equation}
$$

</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> smc workflow </div>

1. Equip each set with a probability distribution: $x \sim \mu_x$ on space region; $t \sim \mu_t$ on time interval, $u_0 \sim \mu_0$ on $U_0$ and $u_b \sim \mu_b$ on $U_b$.
2. Outer loop (parameters): sample $\left( x_i, t_i, u_0^{(i)}, u_b^{(i)}\right)$ with $u_0(x) = \sum\limits_{k=1}^K \alpha_k \phi_k(x), u_b(x) = \sum\limits_{l=1}^L \beta_l \psi_l(x)$.
3. Inner loop (sample paths): For each outer sample simulate $M$ SDE trajectories and estimate $\hat{u}\left( x_i, t_i; u_0^{(i)}, u_b^{(i)}\right)$.

</div>

---

<!-- _class: part4 -->

<div class="block problem">
<div class="label"> to do </div>

* What kind of probalistic guarantee to prove?

$$
\Pr \left[ \Pr_{\left( x, t; u_0, u_b \right)} (u(x, t) \models \varphi) \ge 1 - \epsilon \right] \ge 1 - \delta
$$

* How to optimize sampling (reflection, Hoeffding bound)?
* Compare with analytic solution (if exists), numerical methods, PDE barrier certificate (if possible).
* Verify temporal logic specifications.

</div>

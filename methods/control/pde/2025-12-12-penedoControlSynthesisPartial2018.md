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
        content: 'Friday Morning Seminar — December 12, 2025';
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
        content: 'Part 2: Discretization of PDE using FEM';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part3::before {
        content: 'Part 3: Discretization of STL for PDEs';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
math: mathjax

---

<!-- _class: lead gaia -->

# Control Synthesis for Partial Differential Equations from Spatio-Temporal Specifications

Francisco Penedo, Harold Park and Calin Belta

[CDC2018 Paper](https://www.franpenedo.com/publication/cdc2018/cdc2018.pdf)

---

<!-- _class: gaia -->

## Outline

* Preliminaries
* Discretization of PDE using FEM
* Discretization of STL for PDEs

---

<!-- _class: part1 -->

<div class="block definition">
<div class="label"> definition </div>

<strong>Signal Temporal Logic</strong>

$$ \varphi ::= p \mid \neg \varphi \mid \varphi_1 \land \varphi_2 \mid \varphi_1 \lor \varphi_2 \mid \varphi_1 \mathbf{U}_{[a, b]} \varphi_2$$

<strong>Spatial Predicate</strong> $\lambda \in \Lambda$ is defined as a tuple $(Q_\lambda, X_\lambda, \mu_\lambda, D_\lambda)$ of syntax $Q_\lambda x \in X_\lambda: D_\lambda u(x) - \mu_\lambda(x) > 0$

$Q_\lambda \in \{ \forall, \exists \}$, $X_\lambda \in \overline{\Omega}$ is a closed spatial domain, $\mu_\lambda: X_\lambda \to \mathbb{R}$ is the reference profile and $D_\lambda$ is a differential operator.

$u : \Omega \times[0, T] \to \mathbb{R} \quad u[t] \models \lambda \Leftrightarrow D_\lambda u(x, t) − \mu_\lambda(x) > 0, \forall / \exists x \in X_\lambda$.

</div>

---

<!-- _class: part1 -->

<div class="block definition">
<div class="label"> definition </div>

<strong>Quantitative Semantics</strong>

$$ Q_\lambda = \forall \qquad r(\lambda, u, t) = \min\limits_{x \in X_\lambda} \left( D_\lambda u(x) - \mu_\lambda(x) \right) $$

$$ Q_\lambda = \exists \qquad r(\lambda, u, t) = \max\limits_{x \in X_\lambda} \left( D_\lambda u(x) - \mu_\lambda(x) \right) $$

</div>

---

<!-- _class: part1 -->

<div class="block example">
<div class="label"> example </div>

<strong>Metallic Rod</strong>

Consider a metallic rod of $100 \ \mathrm{mm}$, we want the temperature distribution of the rod to be within $3 \mathrm{K}$ of the linear profile $\mu(x) = x / 4 + 300$ at all times between $4$ and $5$ seconds in the section between $30$ and $60 \ \mathrm{mm}$. Furthermore, the temperature should never exceed $345 \mathrm{K}$ at the point where the heat source is applied ($x = 100$).

$$
\begin{split}
    \phi = & \mathbf{G}_{[4,5]} \left( \left( \forall x \in [30, 60]: u(x) - (x / 4 + 303) < 0 \right) \land \left( \forall x \in [30, 60]: u(x) - (x / 4 + 297) > 0 \right) \right)  \\
    & \land \mathbf{G}_{[0,5]} \left( \forall x \in [100, 100]: u(x) - 345 < 0 \right)
\end{split}
$$

</div>

---

<!-- _class: part1 -->

Given a PDE $\Sigma$, an initial value $u_0$, prescribed boundary conditions $g_d$ and $g_n$, an STL formula $\psi$ over a set of predicates $\Lambda$, and admissible control sets $V_D$ and $V_N$, synthesize control inputs $v_D \in V_D$ and $v_N \in V_N$ such that the trajectory of $\Sigma(u_0, g_d, g_n, v_D, v_N)$ satisfies $\psi$.

$$
\begin{cases}
    f \left( x, t, u, \dfrac{\partial u}{\partial t}, \dfrac{\partial u}{\partial x_i}, \cdots \right) = 0, \ \forall (x, t) \in \Omega \times (0, T)  \\
    u(x, t) = g_d(x, t), \ \forall (x, t) \in \partial \Omega_d \times (0, T) \\
    \dfrac{\partial u}{\partial n}(x, t) = g_n(x, t), \ \forall (x, t) \in \partial \Omega_n \times (0, T) \\
    u(x, t) = v_D(x, t), \ \forall (x, t) \in \partial \Omega_D \times (0, T) \\
    \dfrac{\partial u}{\partial n}(x, t) = v_N(x, t), \ \forall (x, t) \in \partial \Omega_N \times (0, T)
\end{cases}
$$

---

<!-- _class: part2 -->

Use *Galerkin finite element method*, $x_i$ patition points on $\overline{\Omega}$, $d_i = u(x_i, \cdot)$ on 1-D heat equation $u_t = k u_{xx}$:

$$
N_i(x) =
\begin{cases}
    \dfrac{x - x_{i - 1}}{x_i - x_{i - 1}} \quad i > 0, \ x \in [x_{i - 1}, x_i] \\
    \dfrac{x_{i + 1} - x}{x_{i + 1} - x_i} \quad i < n + 1, \ x \in [x_i, x_{i + 1}]
\end{cases}
$$

Then $u^d(x, t) = \sum\limits_{i = 0}^{n + 1} N_i(x) d_i(t)$

$$
\begin{cases}
    M \dot{d} + Kd = F(g) \\
    d_i(0) = u_0(x_i) \quad i = 1, \cdots, n
\end{cases} \tag{5}
$$

---

<!-- _class: part3 -->

## Step 1: $\Sigma \to \Sigma_\text{FEM}$

<div class="block theorem">
<div class="label"> assumption 1 </div>

$u(x, t)$ the trajectory of $\Sigma$;
$u^d(x, t)$ the piecewise linear piecewise linear approximation obtained by interpolating the trajectory of $\Sigma_\text{FEM}$;

$$ \left| \dfrac{\partial^i}{\partial x^i} u(x, t) - \dfrac{\partial^i}{\partial x^i} u^d(x, t) \right| \le \epsilon^i(x, t) $$

perturbation of $\lambda$ by $\delta$, $\lambda^\delta$ given by $(Q_\lambda, X_\lambda, \mu_\lambda + \delta(x), D_\lambda)$

</div>

---

<!-- _class: part3 -->

$\psi$ is perturbed with $\delta = \left\{ \delta_i \right\}_{i = 0, 1, \cdots}$ to be $\psi^\delta$

If $\lambda$ is not preceded by a negation operator and $D_\lambda =  \dfrac{\mathrm{d}^i}{\mathrm{d} x^i}$, then it is substituted by $\lambda^{\delta_i}$. Otherwise it is substituted by $\lambda^{- \delta_i}$

<div class="block theorem">
<div class="label"> theorem 1 </div>

If $u_d \models \psi^\delta$ with $\delta_i(x) = \max\limits_{t} \epsilon^i(x, t)$, then $u \models \psi$.

</div>

---

<!-- _class: part3 -->

<div class="block proof">
<div class="label"> proof </div>

$$
\begin{split}
    \delta_i(x) \ge & \left| \dfrac{\partial^i}{\partial x^i} u(x, t) - \dfrac{\partial^i}{\partial x^i} u^d(x, t) \right| \\
    = & \left| \left( \dfrac{\partial^i}{\partial x^i} u(x, t) - \mu_\lambda(x) \right) - \left( \dfrac{\partial^i}{\partial x^i} u^d(x, t) - \mu_\lambda(x) \right) \right|
\end{split}
$$

Thus $\dfrac{\partial^i}{\partial x^i} u(x, t) - \mu_\lambda(x) \ge \dfrac{\partial^i}{\partial x^i} u^d(x, t) - \mu_\lambda(x) - \delta_i(x) > 0$ which proves $\psi = \lambda$. For $\psi = \neg \lambda$ we have $\dfrac{\partial^i}{\partial x^i} u(x, t) - \mu_\lambda(x) \le \dfrac{\partial^i}{\partial x^i} u^d(x, t) - \mu_\lambda(x) + \delta_i(x) < 0$.

</div>

---

<!-- _class: part3 -->

## Step 2: Fit into $\Sigma_\text{FEM}$ nodes

<div class="block theorem">
<div class="label"> assumption 2 </div>

$\Lambda^\delta_{FEM} = \{\alpha_{\lambda, e} \mid \lambda \in \Lambda^\delta, e \in E^\lambda\} \cup \{\beta_{\lambda, j} \mid \lambda \in \Lambda^\delta, j \in J^\lambda, D_\lambda = \mathrm{id} \}$

$d[t] \models \alpha_{\lambda, e} \Leftrightarrow D_\lambda u^d(x_e^m, t) - \mu_\lambda(x_e^m) > 0$ where $x_e^m = \dfrac{x_e + x_{e+1}}{2}$

$d[t] \models \beta_{\lambda, j} \Leftrightarrow d_j(t) - \mu_\lambda(x_j) > 0$

$\alpha_{\lambda, e}^k$: perturbed $\alpha_{\lambda, e}$ by $k$ like that in *Assumption 1*

</div>

---

<!-- _class: part3 -->

$\psi^d$ is perturbed with $\eta = \left\{ \eta_i \right\}_{i = 0, 1, \cdots}$ to be $\psi^{\delta, \eta} = \bigoplus\limits_{e \in E^\lambda} \gamma_{\lambda, e} \oplus \bigoplus\limits_{j \in J^\lambda} \beta_{\lambda, j}$

If $\lambda$ is not preceded by a negation operator and $D_\lambda = \dfrac{\mathrm{d}^i}{\mathrm{d} x^i}$, then $\gamma_{\lambda, e} = \alpha_{\lambda, e}^{- k_e^\lambda}$. Otherwise $\gamma_{\lambda, e} = \alpha_{\lambda, e}^{k_e^\lambda}$ where $k_e^\lambda = \dfrac{x_{e + 1} - x_e}{2} \left( \max\limits_{c \in [x_e, x_{e + 1}]} \left| \mu^\prime_\lambda(c)\right| + \max\limits_{c \in [x_e, x_{e + 1}]} \eta_i(c) \right)$.

<div class="block theorem">
<div class="label"> theorem 2  </div>

If $d \models \psi^{\delta, \eta}$ with $\eta_i(x) = \max\limits_{t} \left| \dfrac{\partial^{i + 1}}{\partial x^i} u^d(x, t) \right|$, then $u^d \models \psi^\delta$.

</div>

---

<!-- _class: part3 -->

## Step 3: Discretize time

$$
\begin{cases}
    \tilde{d}^{k + 1} = \tilde{A} \tilde{d}^k + \tilde{b}(g) \\
    \tilde{d}^0 = d(0)
\end{cases} \tag{16}
$$

where $\tilde{A} = e^{A \Delta t}$ and $\tilde{b}(g) = - e^{A \Delta t} A^{-1}(e^{ - A \Delta t} - I) b(g)$.

We can compute $(5)$ using numerical integration with approximation error $\max\limits_{k} |d(k \Delta t) - \tilde{d}^k| \le \epsilon^d$

---

<!-- _class: part3 -->

$\psi^{\delta, \eta}$ is perturbed with $\nu = (\nu^y, \nu^d)$ to be $\psi^{\delta, \eta, \nu}$, similar to step 2.

$\gamma_{\lambda, e}$ is perturbed with $\Delta t \nu_{i, e}^y$ and $\beta_{\lambda, j}$ is perturbed with $\Delta t \nu_j^d$.

<div class="block theorem">
<div class="label"> theorem 3  </div>

If $\tilde{d} \models \psi^{\delta, \eta, \nu}$ with $\nu^d_j \ge \max\limits_{t} \left| \dot{d}_j(t) \right| + \epsilon^d$ and $\nu^y_{i,e} \ge \max\limits_{t} \left| \dfrac{\mathrm{d}}{\mathrm{d} t} \dfrac{\partial^i}{\partial x^i} u^d(x_e^m, t) \right| + \dfrac{\partial^i}{\partial x^i} u^{\epsilon_d}(x_e^m, t)$ then $d \models \psi^{\delta, \eta}$.

</div>

---

<!-- _class: part3 -->

$$
\begin{split}
    r_m = \max r(\psi^{\delta, \eta, \nu}, \tilde{d}, 0) \\
    \text{s.t.} (16), v_D \in V_D, v_n \in v_N
\end{split}
$$

<div class="block definition">
<div class="label"> definition </div>

<strong>Quantitative Semantics</strong>

$$ Q_\lambda = \forall \qquad r(\lambda, u, t) = \min\limits_{x \in X_\lambda} \left( D_\lambda u(x) - \mu_\lambda(x) \right) $$

$$ Q_\lambda = \exists \qquad r(\lambda, u, t) = \max\limits_{x \in X_\lambda} \left( D_\lambda u(x) - \mu_\lambda(x) \right) $$

</div>

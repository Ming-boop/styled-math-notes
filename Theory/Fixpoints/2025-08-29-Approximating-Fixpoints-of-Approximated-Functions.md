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
        content: 'Friday Morning Seminar — Augest 29, 2025';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128);
    }
    section.part1::before {
        content: 'Part 1: Fixpoint and Markov Decision Process';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part2::before {
        content: 'Part 2: Dampened Mann Iteration for Known Functions';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part3::before {
        content: 'Part 3: Approximated Fixpoints of Approximated Functions';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part4::before {
        content: 'Part 4: Value Functions of MDPs and Probabilistic Systems';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
    section.part5::before {
        content: 'Part 5: Reference';
        position: absolute;
        top: 10px;
        left: 20px;
        font-size: 30px;
        color:rgb(128, 128, 128); 
    }
math: mathjax

---

<!-- _class: lead gaia -->

# Approximating Fixpoints of Approximated Functions

Paolo Baldan, Sebastian Gurke, Barbara König, Tommaso Padoan, and Florian Wittbold

[CAV 2025 Conference Paper](https://link.springer.com/chapter/10.1007/978-3-031-98679-6_9)

---

<!-- _class: gaia -->

## Outline

* Fixpoint and Markov Decision Process
* Dampened Mann Iteration for Known Functions
* Approximated Fixpoints of Approximated Functions
* Value Functions of MDPs and Probabilistic Systems

---

<!-- _class: part1 -->

<div class="block definition">
<div class="label"> definition 2.1 & 2.2 </div>

<strong>(Markov decision process)</strong> $M = (S, A, T, R)$
$S$ and $A$ are _finite_ sets of states and actions.
$T: S \times A \times S \to [0, 1]$ provides transition probability.
$R: S \times A \times S \to \mathbb{R}_{\ge 0}$ is a non-negative _step-wise_ reward function.

<strong>(Bellman operator)</strong> Given an MDP $M = (S, A, T, R)$ , the _(state-value_) Bellman operator $f_M:\mathbb{R}_{\ge 0}^S\to\mathbb{R}_{\ge 0}^S$: is defined, for $v\in\mathbb{R}_{\ge 0}^S$, by $f_M (v)(s) = \max\limits_{ a\in A(s)} \sum\limits_{s^\prime\in S} T [s, a, s^\prime](R(s, a, s^\prime) + v(s^\prime))$
</div>

---

<!-- _class: part1 -->

The essential step of finding a policy that optimizes the expected return often lies in finding the (least) fixpoint of a Bellman operator $v^*_M = \mu f_M$. $\mu f$ denotes the least fixpoint of function $f$.

<div class="block example">
<div class="label"> example 1.1~1.3 </div>

(_Non-continuity of the least fixpoint operator_) $f, f_n: [0, 1] \to [0, 1]$ with $f_n(x) = \frac{1}{n} + \left(1 - \frac{1}{n} \right)x, f(x) = x$
(_Kleene over improving approximations fails_) $g, g_n: [0, 1] \to [0, 1]$ with $g_n(x) = \frac{1}{n}$ if $x < \frac{1}{n}$ otherwise $g_n(x) = x$ and $g(x) = x$
(_Approximations might not have fixpoints_) $h, h_n: \mathbb{R}_{\ge 0} \to \mathbb{R}_{\ge 0}$ with $h_n(x) = x + \frac{1}{n}, h(x) = x$
</div>

---

<!-- _class: part2 -->

<div class="block theorem">
<div class="label"> assumption 1 </div>

Given a _closed_ and _convex_ set $X \subseteq \mathbb{R}^d_{\ge 0}$ with $0 \in X$ , where  $d \in \mathbb{N}_0$ is a fixed arity, let $f : X \to X$ be _monotone_ and _non-expansive_ with $\text{Fix}(f) \neq\emptyset$.
</div>

(_Mann iteration_) $x_{n+1} = \alpha_n x_n + (1 - \alpha_n) f(x_n)$

(_dampened_ Mann iteration) $x_{n+1} = (1 - \beta_n) \cdot (\alpha_n x_n + (1 - \alpha_n) f(x_n))$

---

<!-- _class: part2 -->

<div class="block theorem">
<div class="label"> lemma 3.1 </div>

(**Existence of least fixpoints**) Let $X \subseteq \mathbb{R}^d_{\ge 0}$ be a _closed_ set with $0 \in X$ and let $f: X \to X$ be a _monotone_ function with $\text{Fix}(f) \neq \emptyset$. Then $f$ has a least fixpoint $\mu f$. Moreover, if $f$ is continuous then $\mu f = \sup\limits_{n\in\mathbb{N}} f^n(0)$.
</div>

---

<!-- _class: part2 -->

<div class="block definition">
<div class="label"> definition 3.2 </div>

(**Mann scheme**) A _(dampened) Mann scheme_ $\mathcal{S}=((\alpha_n)_{n\in\mathbb{N}_0},(\beta_n)_{n\in\mathbb{N}_0})$ is a pair of _parameter sequences_ in $[0,1)$ such that
$$\lim\limits_{n \to \infty} \beta_n = 0, \quad \sum\limits_{n \in \mathbb{N}_0} \beta_n = \infty$$
A _Mann scheme_ is called a **Mann-Kleene scheme** if $\lim\limits_{n \to \infty} \alpha_n = 0$ and a **relaxed** _Mann-Kleene scheme_ if $\lim\limits_{n \to \infty} \alpha_n < 1$.
</div>

---

<!-- _class: part2 -->

Given a _convex_ $X \subseteq \mathbb{R}_{\ge 0}^d$ with $0 \in X$, a _Mann scheme_ $\mathcal{S}$ defines a sequence $(T_n^\mathcal{S})_{n \in \mathbb{N}_0}$ of operators $T_n^\mathcal{S}: (X \to X)\times X\to X$ given by
$$T_n^\mathcal{S}(f, x) = (1 - \beta_n) \cdot (\alpha_n x + (1 - \alpha_n) f(x))$$
Together with a sequence $(f_{n})_{n \in \mathbb{N}_{0}}$ of functions $f_n: X \to X$ and an initial point $x_0 \in X$, it gives rise to a _(dampened) Mann iteration_ $\mathcal{F} = (\mathcal{S}, (f_n), x_0)$, determining a sequence $\left(x_n^\mathcal{F} \right)_{n \in \mathbb{N}_0}$ defined as
$$x_0^\mathcal{F} = x_{0}, \quad x_{n+1}^\mathcal{F} = T_{n}^\mathcal{S} (f_{n}, x_n^\mathcal{F})$$

---

<!-- _class: part2 -->

<div class="block definition">
<div class="label"> definition 3.3 </div>

(**Exact Mann scheme**) Let $\mathcal{S} = ((\alpha_n), (\beta_n))$ be a _Mann scheme_. We call it **exact** if for all $f:X \to X$ as in _Assumption 1_ and $x_0 \in X$,  the sequence $\left(x_n^\mathcal{F} \right)$ generated by the iteration $\mathcal{F} = (\mathcal{S}, f, x_0)$ converges to $\mu f$.
</div>

<div class="block theorem">
<div class="label"> theorem 3.4 &  corollary 3.5</div>

(**Approximating the fixpoint of known functions**) Every _Mann-Kleene scheme_ and _relaxed_ _Mann-Kleene scheme_ $\mathcal{S}$ is _exact_.
</div>

---

<!-- _class: part3 -->

<div class="block theorem">
<div class="label"> assumption 2 </div>

Given a _closed_ and _convex_ set $X \subseteq \mathbb{R}^d_{\ge 0}$ with $0 \in X$ , where  $d \in \mathbb{N}_0$ is a fixed arity, let $(f_{n})$ be a sequence of _monotone_ and _non-expansive_ functions $f_n: X \to X$, pointwise converging to $f: X\to X$ with $\text{Fix}(f) \neq \emptyset$.
</div>

---

<!-- _class: part3 -->

<div class="block theorem">
<div class="label"> lemma 4.1 </div>

Under _Assumption 2_, given a _relaxed Mann-Kleene scheme_ $\mathcal{S}$ and arbitrary $x_0 \in X$, consider the iteration $\mathcal{F} = (\mathcal{S}, (f_n), x_0)$. If $\left(x_n^\mathcal{F} \right)$ is bounded, we have $\liminf\limits_{ n \to \infty } x_n^\mathcal{F} \ge \mu f$.
</div>

Moreover, restricting to the one-dimensional case, i.e., for functions $f_n: X \to X$ with $X \subseteq \mathbb{R}_{\ge 0}$, the condition $\lim\limits_{n \to \infty} \mu f_n = \mu f$, ensures convergence for every (relaxed) Mann-Kleene scheme.

---

<!-- _class: part3 -->

<div class="block theorem">
<div class="label"> theorem 4.2 </div>

(**Approximated Mann-Kleene - one dimension**) Let $f, f_n: X \to X$ be as in _Assumption 2_ with $d = 1$, let $\mathcal{S}$ be a _(relaxed) Mann-Kleene scheme_, $x_0 \in X$, and consider the iteration $\mathcal{F} = (\mathcal{S}, (f_n), x_0)$.  If $\lim\limits_{n \to \infty} \mu f_{n} = \mu f$ then $\left(x_n^\mathcal{F} \right)$ converges to $\mu f$.
</div>

---

<!-- _class: part3 -->

<div class="block example">
<div class="label"> example 4.3 </div>

(_Essence of_ $\mu f_{n} \to \mu f$) Consider $f,f_{n}:[0,1] \to [0,1]$ where $f(x)=x$ and $f_{n}(x) = \left( 1 - \dfrac{1}{n} \right) x + \dfrac{1}{n}$. We choose $\alpha_{n} = 0, \beta_{n} = \dfrac{1}{n}$ and starting with $x_1 = 0$. This generates a sequence $x_n^\mathcal{F} = \dfrac{1}{2}-\dfrac{1}{2(n-1)}$ for $n \ge 2$ and $x_n^\mathcal{F} \to \dfrac{1}{2} \neq \mu f = 0$.
</div>

---

<!-- _class: part3 -->

<div class="block example">
<div class="label"> example 4.4 </div>

(**Divergence on multidimensional cases**) Let $f:[0,1]^2 \to [0,1]^2$ be the flip map $f(x,y)=(y,x)$ and a sequence of maps
$$f_{n} =
\begin{cases}
    (y \ominus \epsilon_n, x \oplus \epsilon_n), & n \text{ is odd} \\
    (y,x), & n \text{ is even}
\end{cases}$$
where $\oplus, \ominus$ stand for truncated addition/subtraction in the interval $[0,1]$ and $\epsilon_n = \frac{2}{n}$.
Moreover, $f_n$ converges (uniformly) to $f$, and since $f_n(0,\epsilon_n) = (0,\epsilon_n)$ we also have $\lim\limits_{n \to \infty} \mu f_n = \mu f = (0,0)$.
</div>

---

<!-- _class: part3 -->

<div class="block example">
<div class="label"> example 4.4 </div>

If we choose the _Mann-Kleene scheme_ with $\alpha_{n} = 0, \beta_{n} = \dfrac{1}{n+1}$ and start from $x_{1}^\mathcal{F}=(0,1)$, we obtain
$$\begin{cases} x_{2n}^\mathcal{F}=\left(0,\dfrac{2n-1}{2n}\right) \\ x_{2n+1}^\mathcal{F}=\left(\dfrac{2n-1}{2n+1},0 \right)\end{cases}$$
The sequence $\left(x_n^\mathcal{F}\right)$ is not even asymptotically regular.
</div>

---

<!-- _class: part3 -->

<div class="block theorem">
<div class="label"> theorem 4.5 </div>

(**Approximated Mann-Kleene - power contractions**) Let $f, f_n : X \to X$ be as in _Assumption 2_ with $f$ a power contraction. Given a _Mann-Kleene scheme_ $\mathcal{S}$ and $x_0 \in X$, consider the iteration $\mathcal{F} = (\mathcal{S}, (f_n), x_0)$.  Then, if $\left(x_n^\mathcal{F}\right)$ is bounded, it converges to the unique fixpoint of $f$.
</div>

It provides a first positive result for the multidimensional case when the limit function $f$ is a power contraction ($f^n$ is a contraction).

---

<!-- _class: part3 -->

<div class="block theorem">
<div class="label"> main theorem 4.6 </div>

(**Approximated Mann iteration**) Let $f, f_n : X \to X$ be as in _Assumption 2_, let $\mathcal{S}$ be an _exact Mann scheme_, $x_0 \in X$, and consider the iteration $\mathcal{F} = (\mathcal{S}, (f_n), x_0)$.  Then $\left(x_n^\mathcal{F}\right)$ converges to $\mu f$ if either
_1._ the sequence $(f_n)$ is monotone and $\lim\limits_{n \to \infty} \mu f_{n} = \mu f$;
_2._ the sequence $(f_n)$ converges normally to $f$, i.e. $\sum\limits_{n \in \mathbb{N}_{0}} \lVert f - f_{n} \rVert < \infty$.
</div>

It provides sufficient conditions on the approximation sequence $(f_n)$, ensuring that an _exact Mann scheme_ converges to the least fixpoint when iterating with approximations.

---

<!-- _class: part4 -->

Main application: approximating the optimal value functions of MDPs in a model-based reinforcement learning setting.
Let $T_n$ be the maximum likelihood approximation to $T$ after performing $n$ random experiments on each state-action pair:
$$T_n(s, a, s^\prime) = \dfrac{\lvert \{i \in \{1, \cdots, n \} \mid X_i^{s, a} = s^\prime \} \rvert}{n}$$

<div class="block theorem">
<div class="label"> assumption 3 </div>

Let $M = (S, A, T, R)$ be an MDP with finite value, $(M_n)$ be given by a sampling process as described above with $T_n \to T$. We denote by $f_n, f: \mathbb{R}^S_{\ge 0} \to \mathbb{R}^S_{\ge 0}$, the Bellman operators of $M_n$ and $M$ respectively.
</div>

---

<!-- _class: part4 -->

<div class="block definition">
<div class="label"> definition 5.1 </div>

(**Regular Mann scheme**) A _Mann scheme_ $\mathcal{S}$ is regular if
_1._ it is exact and
_2._ in the approximated setting (_Assumption 2_), the sequence $\left(x_n^\mathcal{F} \right)$ generated by the iteration $\mathcal{F} = (\mathcal{S}, f, x_0)$ converges to $\mu f$ for all $x_0 \in X$, provided that it is bounded and $f$ is a power contraction.
</div>

By _Theorem 3.4_ and _Theorem 4.5_, all Mann-Kleene schemes are regular, while it is currently unknown whether all relaxed Mann-Kleene schemes are regular.

---

<!-- _class: part4 -->

<div class="block definition">
<div class="label"> definition 5.2 </div>

(**Maximal end-components**) Given MDP $M = (S, A, T, R)$, a tuple $(S^\prime, A^\prime)$ identifying a subprocess $M^\prime = (S^\prime, A^\prime, T^\prime, R^\prime)$ is called an _end-component_ if
_1._ $T^\prime(s, a, s^\prime) = T(s, a, s^\prime),\quad R^\prime = R \mid_{S^\prime \times A^\prime \times S^\prime}, \quad \text{Final}(M) = \emptyset$
_2._ $\forall s \in S^\prime, s^\prime \in S \setminus S^\prime, a \in A^\prime(s), T(s, a, s^\prime) = 0$
_3._ for all $s, s^\prime \in S^\prime$ there exists a path in $M^\prime$ from $s$ to $s^\prime$.

Let $\text{MEC}(M)$ denote the set of all maximal end-components of the MDP $M$ and an MDP $M$ is _simple_ if $\text{MEC}(M) = \emptyset$.
</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> theorem 5.3 </div>

(**Convergence for simple MDPs**) Let $M, M_n$ be MDPs as in _Assumption 3_. Given a _regular Mann scheme_ $\mathcal{S}$ and an initial point $v \in \mathbb{R}^S_{\ge 0}$, consider the iteration $\mathcal{F} = (\mathcal{S}, (f_n), x_0)$. If $M$ is simple, then $x_n^\mathcal{F} \to v^∗_M$.
</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> lemma 5.4 </div>

**(Characterising MDPs with finite value**) An MDP $M$ has a finite value, i.e., its Bellman operator $f_M$ has a (least) fixpoint, if and only if  for all maximal end-components $(S^\prime, A^\prime)$ and all $s, s^\prime \in S^\prime, a \in A^\prime(s)$ with $T(s, a, s^\prime) > 0$, we have $R(s, a, s^\prime) = 0$.
</div>

To deal with quotient, given a function $r: S \to Q$, define
Reindex $r^*: \mathbb{R}^Q_{\ge 0} \to \mathbb{R}^S_{\ge 0}$, for all $w \in \mathbb{R}^Q_{\ge 0}, r^*(w) = w \circ r$;
Maximum $\max\limits_{r}: \mathbb{R}^S_{\ge 0} \to \mathbb{R}^Q_{\ge 0}$, for all $v \in \mathbb{R}^Q_{\ge 0}$ and $e \in Q, \max\limits_{r}(v)(e) = \max \{v(s) \mid s \in S \land r(s) = e \}$.

---

<!-- _class: part4 -->

<div class="block definition">
<div class="label"> definition 5.5 </div>

(**Quotient MDP**) Given $M = (S, A, T, R)$ with finite value, let $\equiv$ denote the equivalence identifying states in the same $\text{MEC}$. Consider $r: S \to S/ \equiv$ defined by mapping each state $s$ to its equivalence class $[s]$. The quotient MDP is $M_r = (S_r, A_r, T_r,R_r)$ where $S_r = S/ \equiv, A_r([s]) = \bigcup\limits_{s^\prime \in [s]} A(s^\prime)$. Moreover, for $s, s^\prime \in S, a \in A(s)$:  
$$T_r([s], a, [s^\prime]) = \sum\limits_{s^{\prime \prime} \in [s^\prime]} T (s, a, s^{\prime \prime}), \quad R_r([s], a, [s^\prime]) = \dfrac{\sum\limits_{s^{\prime \prime} \in [s^\prime]} T(s, a, s^\prime)R(s, a, s^\prime)}{T_r([s], a, [s^\prime])}$$

---

<!-- _class: part4 -->

<div class="block definition">
<div class="label"> definition 5.5 </div>

The reduced quotient $\hat{M}_r$ is defined as the MDP derived from $M_r$ by removing self-loops, i.e., modifying the definition of the set of enabled actions as $\hat{A}_r([s]) = A_r([s]) \setminus \{a \in A_r([s]) \mid T_r([s], a, [s]) = 1 \}$, and adapting $\hat{T}_r$ accordingly.
</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> proposition 5.6 </div>

**(Fixpoints of non-discounted MDPs**) Let $M$ be an  MDP with finite value. Let $\hat{M}_r$ be the reduced quotient. Then $v^∗_M = r^*\left(v^∗_{\hat{M}_r} \right)$.
</div>

<div class="block theorem">
<div class="label"> theorem 5.7 </div>

Under _Assumption 3_, we have $\lim\limits_{n \to \infty} \mu f_n = \mu f$.
</div>

---

<!-- _class: part4 -->

<div class="block theorem">
<div class="label"> main theorem 5.8 </div>

(**Mann iteration over MDPs**) Let $M, M_n$ be MDPs as in _Assumption 3_, given a _regular Mann scheme_ $\mathcal{S}$ and any $v \in \mathbb{R}^S_{\ge 0}$, consider the iteration $\mathcal{F}^a = (\mathcal{S}, (f_n), x_0)$, generating a sequence $\left(v^{\mathcal{F}^a}_n \right)$. Then $\lim\limits_{n \to \infty} v^{\mathcal{F}^a} = v^*_M = \mu f_M$.
</div>

---

<!-- _class: part4 -->

## The Main Algorithm

**Input**: A map $n \mapsto f_n$ such that $(f_n)$ uniformly converges to $f$ almost surely. Parameters $\gamma_i, \delta_i \in \mathbb{R}_{\ge 0}$ such that $\gamma_i > 0$ and $\sum\limits_{i \in \mathbb{N}} \gamma_i < \infty$(analogously for $\delta_i$).
**Output**: A sequence $(x_i)$ in $\mathbb{R}^d_{\ge 0}$.
Initialize $x_0$ and $i$ to be $0$.
**Repeat the following steps**:
Choose $n_i$ such that $\mathbb{P}[\lVert f_{n_i} − f \rVert \ge \gamma_i] \le \delta_i$;
$x_{n+1} := (1 - \beta_n) \cdot (\alpha_n x_n + (1 - \alpha_n) f(x_n)), \quad i := i + 1$.

---

<!-- _class: part4 -->

When the sequence $(f_n)$ is generated by a sampling process as described above, $n_i$ can often be done by using _Bernstein’s inequality_:
$$\mathbb{P}[\lvert T_n(s, a, s^\prime) − T_n(s, a, s^\prime) \rvert \ge \epsilon] \le 2e^{-2\epsilon^2 n}$$

<div class="block theorem">
<div class="label"> theorem 5.10 </div>

For _exact Mann schemes_ the sequence $(x_i)$ produced by the _algorithm_ converges to the least fixpoint of $f$ almost surely.
</div>

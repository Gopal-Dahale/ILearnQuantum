---
title: "Deterministic MBITE"
date: 2023-06-18
tags: ["QOSF"]
author: ["Gopal Ramesh Dahale", "Radoica Draškić"]
ShowToc: true
bibFile: /content/posts/dmbite/bib.json
categories: []
math: true
---

Paper: {{<cite "dmbite">}}

## Imaginary time evolution (ITE)

ITE is a technique to find the ground state of a system where the time t is replaced by imagainary time $i\tau$. To understand this, let us consider the time-dependent Schrodinger equation for a system with Hamiltonian $H$:

$$
\begin{equation}
    i\frac{\partial}{\partial t} \ket{\psi(t)}= H \ket{\psi(t)}
\end{equation}
$$

where $ \ket{\psi(t)} $ is is the state of the system at time $t$, and $H$ is the Hamiltonian of the system. In the case of real time evolutoin, the state of the system at time $t$ is given by $ \ket{\psi(t)} = e^{-iHt}\ket{\psi(0)}$ where $\ket{\psi(0)}$ is the initial state of the system.

In the case of imaginary time evolution, the modified Schrodinger equation is given by:

$$ \frac{\partial}{\partial \tau}\ket{\psi(\tau)} = -H\ket{\psi(\tau)} $$

The solution to this equation is given by $ \ket{\psi(\tau)} = e^{-H\tau}\ket{\psi(\tau)} $,

This transfomration makes it easier to find the ground state of the system. As $\tau \rightarrow \infty$, the state of the system converges to the ground state of the system.
$$
\begin{equation}
    \lim_{\tau \rightarrow \infty} \ket{\psi(\tau)} = \ket{E_0}
\end{equation}
$$
where $\vert E_0\rangle$ is the ground state of $H$.

## Theory
### Measurement operators
The strategy is to construct measurement operators which take the form of the exponentiated hamiltonian in equation 2. This can be achieved by performing a weak measurement of $H$. Consider a n-qubit system with an ancilla qubit a which will be used for the weak measurement via the hamiltonian of $H \otimes Y$. The ancilla qubit is initially prepared in state $\ket{+}$. Performing time evolution for $\epsilon$ time we have:

$$
\begin{align*}
e^{-i \epsilon H \otimes Y} \ket{\psi(0)} \ket{+}_a =& \sum_n \bra{E_n}\ket{\psi_0} \ket{E_n} e^{-i \epsilon E_n Y} \ket{+}_a \\\\
=& \frac{1}{\sqrt{2}}\sum_n \bra{E_n}\ket{\psi_0} \ket{E_n} \Bigl[ (\cos E_n \epsilon - \sin E_n \epsilon)\ket{0}_a \\\\
& (\cos E_n \epsilon + \sin E_n \epsilon)\ket{1}_a \Bigr]
\end{align*}
$$

Using the projector operator $\ket{0}\bra{0}$ on the ancilla qubit, we get

$$
\begin{equation}
    \frac{1}{\sqrt{2}}\sum_n \bra{E_n}\ket{\psi_0} (\cos E_n \epsilon - \sin E_n \epsilon)\ket{E_n}\ket{0}_a \approx \frac{e^{-\epsilon H}}{\sqrt{2}} \ket{\psi_0}\ket{0}
\end{equation}
$$

while $\ket{1}\bra{1}$ gives

$$
\begin{equation}
    \frac{1}{\sqrt{2}}\sum_n \bra{E_n}\ket{\psi_0} (\cos E_n \epsilon + \sin E_n \epsilon)\ket{E_n}\ket{1}_a \approx \frac{e^{\epsilon H}}{\sqrt{2}} \ket{\psi_0}\ket{0}
\end{equation}
$$

So the measurement operators are

$$
\begin{align}
    M_0 =& \frac{1}{\sqrt{2}}\sum_n (\cos E_n \epsilon - \sin E_n \epsilon)\ket{E_n}\bra{E_n} \approx \frac{e^{-\epsilon H}}{\sqrt{2}} \\\\
    M_1 =& \frac{1}{\sqrt{2}}\sum_n (\cos E_n \epsilon + \sin E_n \epsilon)\ket{E_n}\bra{E_n} \approx \frac{e^{\epsilon H}}{\sqrt{2}}
\end{align}
$$

We can see that the above two equations takes the form of the exponentiated operator in equation 2. The exponential approximation is valid when $||\epsilon H|| \ll 1$.

### Amplification of ground state

Upon measurement of the ancilla qubit we get can a 0 or 1. Consider a particular measurement sequence where there are $k_0$ counts of 0 (i.e. $M_0$ is the measurement operator) and $k_1$ counts of 1 (i.e. $M_1$ is the measurement operator. Since $[M_0, M_1] = 0$, the order of outcomes does not matter and therefore we can write

$$
\begin{equation}
    M_0^{k_0}M_1^{k_1} = \sum_nA_{k_0k_1}(\epsilon E_n) \bra{E_n}\ket{\psi_0}\ket{E_n}
\end{equation}
$$

where the amplitude function is defined as

$$
\begin{equation}
    A_{k_0k_1} = \frac{(\cos x - \sin x)^{k0}(\cos x - \sin x)^{k_1}}{\sqrt{2^{k_0 + k_1}}}
\end{equation}
$$

The amplitude function takes a gaussian form for large number of measurements in the range $-\pi/4 \leq x \leq \pi/4$ and the peak occurs at

$$
\begin{equation}
    x_{k_0k_1}^{max} = \frac{1}{2}\arcsin \left( \frac{k_0 - k_1}{k_0 + k_1} \right)
\end{equation}
$$

For large measurements a collapse of energy basis occurs and the ground state energy can be estimated from $x_{k_0k_1}^{max} \approx \epsilon E_0$, we can approximate the equation \ref{eq:seq} to $e^{-\epsilon(k_0 - k_1)H}$ from the exponential approximation of the equations \ref{eq:m0} and \ref{eq:m1}. From the figure we can see that this exponential approximates only one side of the gaussian and therefore for random outcomes of $k_0, k_1$, the sequence may not result in amplification of ground state.
If the gaussian peak are to the left of ground state, the measurements sequence will produce the desired value of $A_{k_0k_1}$ which will enhance the ground state. To be precise, if $x_{k_0k_1}^{max} < \epsilon (E_0 + E_1)/2$ for any outcome of measurement, then it will have an amplifying effect towards the ground state.

## Methodology
 The strategy is the adjust the peak position of the gaussian near the ground state. The idea is to apply unitary operations conditioned on the measuremnet outcomes. Using a energy threshold $E_{th}$, if the peak is below the threshold then no operation is applied otherwise a corrective unitary is applied. This is peformed iteratively:

$$
 \begin{equation}
     \ket{\psi_{t+1}} = \frac{U_{k_0^{(t+1)}k_1^{(t+1)}}M_n\ket{\psi_t}}{\sqrt{\bra{{\psi_t}}|{M_n^{\dag}M_n}\ket{\psi_t}}}
 \end{equation}
$$

where $n\in \{0,1\}$ and

$$
\begin{equation}
    U_{k_0k_1} =
     \begin{cases}
       I &\quad\text{if} ~ x_{k_0k_1}^{max} < \epsilon E_{th} \\\\
       U_c &\quad\text{otherwise.} \\
     \end{cases}
\end{equation}
$$

and
$$
\begin{equation}
    k_m^{(t+1)} =
     \begin{cases}
       k_m^{(t)} + \delta_{mn} &\quad\text{if} ~ x_{(k_0^{(t)} + \delta_{0n})(k_0^{(t)}  + \delta_{1n})}^{max} < \epsilon E_{th} \\\\
       0 &\quad\text{otherwise.} \\
     \end{cases}
\end{equation}
$$

For ensuring the convergence over iterations to the ground state, $|\bra{E_n}U_c\ket{E_m}| > 0, \forall n,m$ which can be satisfied with a random unitary matrix. The authors considered random intial states and corrective unitary, however a sophisticated choice of initial state and corrective unitary can be made to improve the convergence rate. The code explains the examples shown in the paper in detail.

{{< bibliography cited >}}
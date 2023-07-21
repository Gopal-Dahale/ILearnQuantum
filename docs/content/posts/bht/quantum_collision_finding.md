---
title: "Quantum Collision Finding"
date: 2023-07-21
tags: ["Womanium"]
author: "Gopal Ramesh Dahale"
ShowToc: true
bibFile: /content/posts/bht/bib.json
categories: ["Quantum Cryptanalysis"]
math: true
---

A Womanium Global Media Project Initiative.

## Collisions in hash functions

A collision in a hash function is when two different inputs produce the same output hash. This is a problem because it violates the fundamental premise of a hash function, which is to produce a unique output for each unique input. Collisions can be exploited by attackers to create fraudulent transactions or gain unauthorized access to secure systems, making them a critical concern in the design and implementation of hash functions.

To avoid collisions, hash functions use complex mathematical algorithms to ensure that each input produces a unique output. The quality of a hash function is typically measured by its collision resistance, which refers to its ability to prevent collisions from occurring. Strong collision resistance is important for the security of systems that rely on hash functions, such as digital signatures and cryptographic hash functions used in encryption.

## The BHT Algorithm

The BHT algorithm {{<cite "bht">}} is a generic quantum collision-finding algorithm developed by Brassard, Høyer, and Tapp. The goal is to find a collision of a random function $F : \\{ 0, 1 \\}^n \rightarrow  \\{0, 1\\}^n$.

### Grover's Algorithm and Generalization

Given a function $F: X \rightarrow \\{0,1\\}$ such that there exists a unique $x_0 \in X$ so that $F(x_0) = 1$. The task is to find $x_0$. We know that this requires $O(\sqrt{N})$ evaluations of $F$ where, $N = |X|$ {{<cite "grover">}}.

To generalize this, consider an arbitrary function $F: X \rightarrow Y$. Now, given some $y_0 \in Y$, find $x \in X$ such that $F(x) = y_0$ provided such $x$ exists. If there are $t$ number of solutions i.e. there are $t$ number of such $x$, then it requires $O(\sqrt{N/t})$ evaluations of $F$. From now onwards, we denote this generalization by **Grover**($F, y_0$). This will be handy later.

### What is qRAM?

qRAM is a quantum analogue of RAM which allows to efficiently access stored data in quantum superpositions.

Let there be a list of classical data $L = \\{ x_0, x_1 \dots x_{2^m -1} \\}$ where $x_i\in \\{0,1\\}^n$ for each $i$. Then, the qRAM for $L$ is modeled as an unitary operator $U_{\text{qRAM}}(L)$ defined by

$$
\begin{equation}
    U_{\text{qRAM}}(L): \ket{i} \otimes \ket{y} \rightarrow \ket{i} \otimes \ket{y \oplus x_i}
\end{equation}
$$

for $i\in \\{0,1\\}^m$ and $y\in \\{0,1\\}^n $.

In this context, having qRAM available means that we have a quantum gate capable of realizing the unitary operation (1) for a list $L$ of classical data in addition to access to basic quantum gates {{<cite "collision">}}.

### BHT Algorithm

The algorithm consists of 2 steps

**Step 1**

1. Start by selecting an arbitrary subset $K \subseteq X$ of cardinality $k = 2^{n/3}$. Create a table $L$ where each item in $L$ holds a unique pair $(x, F (x))$ with $x \in K$.
2. Sort $L$ according to the second entry in each item of $L$.
3. Verify whether $L$ contains any collisions, meaning check if there are distinct elements $(x_0, F (x_0)), (x_1, F (x_1)) \in L$ such that $F (x_0) = F (x_1)$. If so, proceed to Step 2.3. If not, the $2^{n/3}$ pairs of $L$ are stored in **qRAM**. The implication is that they can be accessed in quantum superposition.

**Step 2**

1. Calculate $x_1 = \textbf{Grover}(H, 1)$ where $H : X \rightarrow \{0, 1\}$ denotes the function defined by $H(x) = 1$ iff there exists $x_0 \in K$ such that $(x_0 , F (x)) \in L$ but $x \not = x_0$. (It's worth noting that if such $x_0$ exists, it is unique, as we have already verified the absence of collisions in $L$).
2. Search $(x_0 , F (x_1 )) \in L$.
3. Output the collision set $\{x_0 , x_1 \}$.

## Collision Complexity

For the BHT algorithm, the step 1 involves making queries in time $O(2^{n/3})$. Step 2 uses Grover's search and runs in $O\left(\sqrt{2^n/|L|}\right) = O(2^{n/3})$. Therefore, BHT finds a collision in time $\tilde{O}(2^{n/3})$ by making $O(2^{n/3})$ quantum queries, provided that there is access to exponentially large quantum random access memory (qRAM).

### Classical Setting

In the classical setting, the generic attack complexity to find collisions against an n-bit hash function is $O(2^{n/2})$ due to the birthday paradox. As a result, any dedicated attack that finds collisions with less than $O(2^{n/2})$ complexity is considered as a meaningful attack.

### Quantum Setting

In the quantum setting, the complexity of a generic attack depends on the specific assumptions or model of the underlying quantum machines. Regardless of the model, the lower bound of the query complexity is proven to be $\Omega(2^{n/3})$ {{<cite "zhandry">}} and there is an attack matching this bound if $O(2^{n/3})$ qubits are available which is the BHT algorithm. By the same analogy, any dedicated attack with less than $O(2^{n/3})$ quantum complexity should be regarded as a meaningful attack. However, comparing dedicated attacks with the generic attack complexity in the quantum setting requires careful consideration because the generic attack complexity depends on the model of the quantum computations. We'll see this in the next sections.

## Impact of qRAM on BHT

We consider a model of a small quantum computer of polynomial size and a qRAM that allows us to access exponentially many classical data in quantum superposition. Here we do $\textbf{not}$ consider any parallelized computations. In this model, the best collision-finding algorithm is BHT because we can implement it with qRAM (of size $O(2^{n/3})$) and its time complexity matches the tight quantum query complexity $\Theta (2^{n/3} )$.

## Time and memory tradeoff compared to classical algorithms

BHT achieves a time complexity $\tilde{O}(2^{n/3})$, however, it requires a large qRAM of size $\tilde{O}(2^{n/3} )$ as well. This raises concerns since the availability of such qRAM in the future is uncertain, especially in the case of small quantum computers.

Alternatively, one can use a quantum circuit of size $O(2^{n/3})$, but it comes with the trade-off of using an exponentially large number of qubits. This leads to questions about parallelization. In terms of time-space complexity, BHT is less efficient than the classical parallel rho method {{<cite "bernstein">}} {{<cite "prho">}}. When $P$ classical processors are available, the parallel rho method finds a collision in time $O(2^{n/2}/P)$. This means that if a quantum computer of size $2^{n/3}$ is available, but qRAM is not, simply running the parallel rho method on the quantum computer can find a collision in time $2^{n/6}$ which is much faster than BHT.

In the classical setting, the rho method finds a memory-less collision in time $O(2^{n/2})$, which matches the classical tight bound for query complexity. However, there is no known any $\textbf{memory-less}$ quantum collision finding algorithm that matches the optimal query complexity $2^{n/3}$.

Let $S$ denote the size of computational resources required for a quantum algoithm and $S$ is the maximum size of quantum computers and classical memory. Let $T$ denote its time complexity. Then the trade-off $T \cdot S = 2^{n/2}$ given by the parallel rho
method is the best one even in the quantum setting.

## Outlook

In the quantum setting, comparing dedicated attacks with the generic attack complexity requires caution since the generic attack complexity depends on the model of the quantum computations. As we have seen, the BHT cannot outperform the classical computations, considering that each qubit can behave as either processor or memory (by running $2^{n/3}$ processors in parallel, collisions can be found in time $O(2^{n/6})$ even with classical machines). However, if a quantum computer of polynomial size with exponentially large qRAM is available, BHT is the best collision attack. It is challenging to predict which model is more likely to be realized in the future, and it would be valuable to discuss the advantages of attacks in various models with different generic attack complexities.

## References

{{< bibliography cited >}}
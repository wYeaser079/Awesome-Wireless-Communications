# Cell-Free Massive MIMO: Zero Forcing and Conjugate Beamforming Receivers — Detailed Analysis

> **Paper**: "Cell-Free Massive MIMO: Zero Forcing and Conjugate Beamforming Receivers"
> **Authors**: Yao Zhang, Haotong Cao, Meng Zhou, and Longxiang Yang
> **Published**: Journal of Communications and Networks, Vol. 21, No. 6, December 2019
> **DOI**: 10.1109/JCN.2019.000053
> **Analysis Date**: February 2026

---

## Table of Contents

- [1. System Model](#1-system-model)
  - [1.1 What is Cell-Free Massive MIMO?](#11-what-is-cell-free-massive-mimo)
  - [1.2 The Physical Setup](#12-the-physical-setup)
  - [1.3 Why TDD Mode?](#13-why-tdd-mode)
  - [1.4 The Channel Model (Equation 1)](#14-the-channel-model-equation-1)
  - [1.5 The Coherence Interval Structure](#15-the-coherence-interval-structure)
  - [1.6 Uplink Channel Estimation (Equations 2-8)](#16-uplink-channel-estimation-equations-2-8)
  - [1.7 Uplink Data Transmission (Equations 9-11)](#17-uplink-data-transmission-equations-9-11)
  - [1.8 The Fronthaul/Backhaul Challenge](#18-the-fronthaulbackhaul-challenge)
  - [1.9 Summary of System Model](#19-summary-of-system-model)
- [2. ZF (Zero Forcing) Receiver](#2-zf-zero-forcing-receiver)
  - [2.1 What is Zero Forcing? (Fundamental Intuition)](#21-what-is-zero-forcing-fundamental-intuition)
  - [2.2 The ZF Detector Matrix](#22-the-zf-detector-matrix)
  - [2.3 The Received Signal After ZF Processing (Equation 12)](#23-the-received-signal-after-zf-processing-equation-12)
  - [2.4 The SINR Expression (Equation 15)](#24-the-sinr-expression-equation-15)
  - [2.5 Theorem 1: The Tight Approximate Rate Expression (Equation 16)](#25-theorem-1-the-tight-approximate-rate-expression-equation-16)
  - [2.6 The Proof Sketch of Theorem 1](#26-the-proof-sketch-of-theorem-1)
  - [2.7 Key Remarks and Insights](#27-key-remarks-and-insights)
  - [2.8 Power Control: Total Rate Maximization (Equations 21-29)](#28-power-control-total-rate-maximization-equations-21-29)
  - [2.9 Power Control: One User's Rate Maximization (Equations 30-31)](#29-power-control-one-users-rate-maximization-equations-30-31)
  - [2.10 ZF: Practical Considerations and External Insights](#210-zf-practical-considerations-and-external-insights)
  - [2.11 Summary Table: ZF Receiver](#211-summary-table-zf-receiver)
- [3. CB (Conjugate Beamforming) Receiver](#3-cb-conjugate-beamforming-receiver)
  - [3.1 What is Conjugate Beamforming? (Deep Intuition)](#31-what-is-conjugate-beamforming-deep-intuition)
  - [3.2 The CB Detector Matrix](#32-the-cb-detector-matrix)
  - [3.3 The Received Signal After CB Processing (Equation 32)](#33-the-received-signal-after-cb-processing-equation-32)
  - [3.4 The Achievable Rate for CB (Lemma 1, Equation 33)](#34-the-achievable-rate-for-cb-lemma-1-equation-33)
  - [3.5 Dissecting the SINR of CB (Term by Term)](#35-dissecting-the-sinr-of-cb-term-by-term)
  - [3.6 Power Control for CB: Total Rate Maximization (Equations 36-43)](#36-power-control-for-cb-total-rate-maximization-equations-36-43)
  - [3.7 One User's Rate Maximization for CB (Equations 44-45)](#37-one-users-rate-maximization-for-cb-equations-44-45)
  - [3.8 CB: The Distributed Processing Advantage](#38-cb-the-distributed-processing-advantage)
  - [3.9 CB vs. ZF: The Complete Comparison](#39-cb-vs-zf-the-complete-comparison)
  - [3.10 The Big Picture: What This Paper Contributes](#310-the-big-picture-what-this-paper-contributes)
- [4. Architecture Diagrams](#4-architecture-diagrams)
- [5. Recommended Further Learning](#5-recommended-further-learning)

---

# 1. System Model

## 1.1 What is Cell-Free Massive MIMO?

Before diving into the math, let's understand **what problem this paper is solving**.

### Traditional Cellular MIMO vs. Cell-Free Massive MIMO

In **traditional cellular systems**, a geographic area is divided into **cells**. Each cell has a **base station (BS)** at its center that serves users within that cell. The problem? Users at **cell edges** suffer from:
- **Low signal strength** (far from their BS)
- **High inter-cell interference** (close to neighboring BSs)
- **Unfair rate distribution** (center users get much better service than edge users)

**Cell-free massive MIMO** radically changes this architecture:
- There are **NO cell boundaries** at all — the entire network is treated as **one giant cell**
- Many **Access Points (APs)** are spread across the area (like a distributed antenna system)
- **Every user** is served **simultaneously by ALL APs** coherently
- All APs connect to a **Central Processing Unit (CPU)** via backhaul links

This was first proposed in the landmark paper by **Hien Quoc Ngo, Ashikhmin, Yang, Larsson, and Marzetta (2017)**: *"Cell-Free Massive MIMO versus Small Cells"* (IEEE Trans. Wireless Commun., vol. 16, no. 3).

> **Source**: H. Q. Ngo et al., "Cell-free massive MIMO versus small cells," IEEE TWC, 2017.
> Available: https://ieeexplore.ieee.org/document/7827017

The key insight from Emil Bjornson (a leading researcher in this field) is:

> "Cell-free massive MIMO provides uniformly good service to all users, regardless of location."

> **Source**: Emil Bjornson's YouTube channel on Massive MIMO
> https://www.youtube.com/@emaborjeson

---

## 1.2 The Physical Setup

This paper considers:

| Parameter | Description |
|-----------|-------------|
| **M** | Number of Access Points (APs), distributed in the area |
| **K** | Number of single-antenna users |
| **N** | Number of antennas at each AP |
| **Constraint** | M > K (more APs than users — necessary for spatial multiplexing) |
| **Mode** | TDD (Time Division Duplex) — uplink only is studied |

**Key architectural features:**
- Each AP has **N antennas** (multi-antenna AP — this is a generalization over the original cell-free paper [1] which assumed N=1)
- Each user has **only 1 antenna** (single-antenna user terminal)
- All APs use the **same time-frequency block** to serve all users (no frequency reuse partitioning)
- APs have **limited computing power** — they do basic processing locally
- The **CPU** does the heavy lifting: computing detection vectors, power control coefficients, etc.
- APs connect to CPU via a **low-latency backhaul network** (fronthaul)

---

## 1.3 Why TDD Mode?

The system uses **Time Division Duplex (TDD)**, which is critical because of **channel reciprocity**:

In TDD, uplink and downlink share the **same frequency band** but are separated in time. Because the channel is reciprocal (same physical medium), the CPU can estimate the **uplink channel** from pilot signals and use that **same channel estimate for downlink precoding** — without needing separate downlink training.

This is especially important in cell-free systems because:
- With M APs x N antennas each = **MN total antennas** serving K users
- Estimating all channels would require MN x K channel coefficients
- TDD + uplink training makes this feasible (training overhead scales with K, not MN)

> **Source**: T. L. Marzetta, "Noncooperative Cellular Wireless with Unlimited Numbers of Base Station Antennas," IEEE Trans. Wireless Commun., 2010.
> https://ieeexplore.ieee.org/document/5595728

---

## 1.4 The Channel Model (Equation 1)

The channel from user k to AP m is:

```
g_mk = beta_mk^(1/2) * h_mk        ... (1)
```

Where:
- **g_mk** in C^(N x 1) — the **complete channel vector** from user k to AP m (N-dimensional because AP m has N antennas)
- **beta_mk** — the **large-scale fading coefficient** (a scalar, same for all N antennas at AP m)
- **h_mk** in C^(N x 1) — the **small-scale fading vector**, where each entry [h_mk]_n ~ CN(0,1)

### What does this mean physically?

**Large-scale fading (beta_mk)** captures:
- **Path loss**: Signal power decreases with distance (typically proportional to d^(-alpha), where alpha is the path loss exponent, typically 2-5)
- **Shadow fading (log-normal)**: Random obstacles (buildings, trees) cause slow, random variations in received power

**Key property**: beta_mk changes **very slowly** — it remains approximately constant over ~40 coherence intervals. This means you don't need to re-estimate it frequently.

**Small-scale fading (h_mk)** captures:
- **Rayleigh fading**: Due to multipath propagation (signal bouncing off objects), the received signal is the sum of many scattered components. When there's no dominant line-of-sight path, each antenna's channel coefficient follows CN(0,1) — a circularly symmetric complex Gaussian with zero mean and unit variance.
- This changes **rapidly** — on the order of one coherence interval (milliseconds).

### Why this decomposition matters

The separation g_mk = beta_mk^(1/2) * h_mk is fundamental because:
1. **beta_mk** can be estimated over long time periods (statistical averaging)
2. **h_mk** must be estimated in every coherence interval (fast changing)
3. The analysis will ultimately produce rate expressions that depend only on **beta_mk** (not h_mk), making them practically useful for system design

---

## 1.5 The Coherence Interval Structure

Each **coherence interval** of length T (in time-frequency samples) is divided into:

```
|<------- tau symbols ------->|<---- (T - tau) symbols ---->|
|   Uplink Pilot Training      |   Uplink Data Transmission   |
```

- **Phase 1 (tau symbols)**: All users simultaneously send known pilot sequences to the APs -> APs estimate channels
- **Phase 2 (T - tau symbols)**: Users send data -> APs forward received signals to CPU -> CPU detects data using estimated channels

The **pre-log factor** for spectral efficiency is (1 - tau/T), representing the fraction of the coherence interval available for data. Larger tau gives better estimates but wastes more resources on training.

---

## 1.6 Uplink Channel Estimation (Equations 2-8)

This is a **critical** part of the system model. Everything that follows (ZF, CB performance) depends on the quality of channel estimation.

### Step 1: Pilot Transmission (Eq. 2)

Each user k is assigned a pilot sequence **phi_k** in C^(tau x 1) with ||phi_k||^2 = 1.

All K users **simultaneously** transmit their pilots. The m-th AP receives:

```
Y_m,p = sqrt(tau * rho_p) * SUM_{k=1}^{K} g_mk * phi_k^H + W_m,p     ... (2)
```

Where:
- **rho_p** = normalized transmit SNR for pilot signals
- **W_m,p** = N x tau noise matrix (i.i.d. CN(0,1) entries)
- **sqrt(tau * rho_p)** = scaling factor (pilot energy = tau * rho_p)

**Intuition**: Each AP receives a **superposition** of all K users' pilots, corrupted by noise. The pilots act as "signatures" — by correlating with a specific pilot, you can isolate (de-spread) that user's channel.

### Step 2: De-spreading / Matched Filtering (Eq. 3)

The m-th AP projects the received signal onto the k-th user's pilot:

```
Y_tilde_mk,p = Y_m,p * phi_k = sqrt(tau * rho_p) * SUM_{i=1}^{K} g_mi * (phi_i^H * phi_k) + W_m,p * phi_k     ... (3)
```

**If all pilots are orthogonal** (phi_i^H * phi_k = 0 for i != k):
- Only user k's channel g_mk survives -> clean estimation
- This requires tau >= K (enough orthogonal pilots for all users)

**If pilots are NOT orthogonal** (tau < K, the "aggressive" case in this paper):
- Some users share pilots (phi_i^H * phi_k != 0)
- **Pilot contamination** occurs: The estimate of user k's channel is polluted by channels of users using the same/correlated pilots
- This is one of the **fundamental bottlenecks** of massive MIMO

### Step 3: MMSE Channel Estimation (Eq. 4-5)

Using the **Minimum Mean Square Error (MMSE)** principle:

```
g_hat_mk = c_mk * Y_tilde_mk,p     ... (4)
```

where:

```
c_mk = (sqrt(tau * rho_p) * beta_mk) / (tau * rho_p * SUM_{i=1}^{K} beta_mi * |phi_i^H * phi_k|^2 + 1)     ... (5)
```

**What does c_mk do?** It's a **Wiener filter coefficient** — it optimally weighs the noisy observation to minimize the mean squared error between the estimate and the true channel. The denominator includes:
- **tau * rho_p * SUM beta_mi |phi_i^H phi_k|^2**: Total signal power from all users whose pilots correlate with user k's pilot
- **+1**: Noise power (normalized)

### Step 4: Estimation Error (Eq. 6-8)

The estimation error is:

```
g_tilde_mk = g_mk - g_hat_mk     ... (6)
```

Under MMSE, a beautiful property holds — **the estimate and error are uncorrelated**:

```
g_hat_mk ~ CN(0, alpha_mk * I_N)        (estimate)
g_tilde_mk ~ CN(0, (beta_mk - alpha_mk) * I_N)   (error)     ... (7)
```

where:

```
alpha_mk = (tau * rho_p * beta_mk^2) / (tau * rho_p * SUM_{i=1}^{K} beta_mi * |phi_i^H * phi_k|^2 + 1)     ... (8)
```

**Interpretation of alpha_mk**:
- alpha_mk is the **variance of the channel estimate** — it measures how much useful channel information was captured
- (beta_mk - alpha_mk) is the **residual estimation error variance**
- When alpha_mk -> beta_mk: estimation is nearly perfect (low error)
- When alpha_mk -> 0: estimation is terrible (high error)

**When is alpha_mk large (good estimation)?**
- High pilot SNR (rho_p large)
- Long pilot sequence (tau large)
- Strong channel (beta_mk large)
- Low pilot contamination (few users share the same pilot)

---

## 1.7 Uplink Data Transmission (Equations 9-11)

### At each AP (Eq. 9):

```
y_m,u = sqrt(rho_u) * SUM_{k=1}^{K} g_mk * sqrt(eta_k) * s_k + w_m,u     ... (9)
```

Where:
- **s_k** = data symbol from user k (E{|s_k|^2} = 1)
- **eta_k** in [0,1] = **power control coefficient** for user k
- **rho_u** = normalized uplink data SNR
- **w_m,u** = N x 1 AWGN noise vector

### Aggregated at the CPU (Eq. 10):

All M APs forward their received signals to the CPU. Stacking everything:

```
y_u = sqrt(rho_u) * G * P^(1/2) * s + w_u     ... (10)
```

Where:
- **G** = [g_1, ..., g_K] in C^(MN x K) — the **global channel matrix** (stacks all channels from all APs to all users)
- **g_k** = [g_1k^T, ..., g_Mk^T]^T in C^(MN x 1) — the **aggregate channel** from user k to ALL APs
- **P** = diag(eta_1, ..., eta_K) — power control matrix
- **w_u** = [w_1,u^T, ..., w_M,u^T]^T — stacked noise

**Key observation**: The dimension MN x K means the system has **MN total receive antennas** collectively serving K users. Since M > K and N >= 1, we have MN >> K — this is the "massive" in massive MIMO.

### Linear Detection at CPU (Eq. 11):

The CPU applies a **linear detector** A in C^(MN x K):

```
r_k = sqrt(rho_u) * a_k^H * G * P^(1/2) * s + a_k^H * w_u
    = sqrt(rho_u) * SUM_{i=1}^{K} sqrt(eta_i) * a_k^H * g_i * s_i + a_k^H * w_u     ... (11)
```

Where **a_k** is the k-th column of A — the **detection vector** for user k.

**The choice of A defines the receiver type:**
- **A = G_hat** -> **Conjugate Beamforming (CB) / Matched Filter**
- **A = G_hat * (G_hat^H * G_hat)^(-1)** -> **Zero Forcing (ZF)**

### Why Linear Detectors?

The **optimal** detector (maximum likelihood, ML) has exponential complexity O(|constellation|^K). For massive MIMO with many users, this is computationally infeasible. Linear detectors are:
- **Computationally simple** (matrix multiplications and inversions)
- **Near-optimal** when MN >> K (the "massive" regime)
- **Analytically tractable** (we can derive closed-form rate expressions)

> **Source**: E. Bjornson, J. Hoydis, and L. Sanguinetti, "Massive MIMO Networks: Spectral, Energy, and Hardware Efficiency," Foundations and Trends in Signal Processing, 2017.
> https://massivemimobook.com

---

## 1.8 The Fronthaul/Backhaul Challenge

A crucial practical concern (mentioned in the paper's introduction): In cell-free massive MIMO, **all APs must send their received signals to the CPU** through backhaul links. This creates enormous **fronthaul requirements**:

- **For CB receiver**: Each AP can partially process signals **locally** (just multiply by the conjugate of its local channel estimate). Only scalar values need to be sent to the CPU -> **low backhaul requirement**
- **For ZF receiver**: The CPU needs the **full received signal vectors** from all APs, because ZF requires the global channel matrix inverse -> **high backhaul requirement**

This is a key practical trade-off: ZF gives better performance but demands more from the backhaul infrastructure.

> **Source**: M. Bashar et al., "Cell-free massive MIMO with limited backhaul," IEEE ICC 2018.
> https://ieeexplore.ieee.org/document/8422865

---

## 1.9 Summary of System Model

```
Architecture:  M APs (N antennas each) <--> CPU <--> K users (1 antenna each)
Mode:          TDD (uplink channel estimation -> uplink data)
Channel:       g_mk = sqrt(beta_mk) * h_mk (Rayleigh fading)
Estimation:    MMSE -> g_hat_mk with quality parameter alpha_mk
Detection:     Linear detector A applied at CPU to y_u
Goal:          Find achievable uplink rate for ZF and CB receivers
                + optimize power control coefficients eta_k
```

---

# 2. ZF (Zero Forcing) Receiver

## 2.1 What is Zero Forcing? (Fundamental Intuition)

Imagine you're in a crowded room with 10 people talking simultaneously. You want to hear **only person #3**. You have two strategies:

1. **Matched Filter / CB approach**: Turn your ear toward person #3 and listen as hard as you can. You'll hear person #3 louder, but you'll also hear everyone else. The noise from others doesn't go away.

2. **Zero Forcing approach**: Use noise-cancelling technology that **specifically identifies and cancels** the voices of persons #1, #2, #4, ..., #10. After cancellation, you hear ONLY person #3 in perfect silence — but the cancellation process might amplify some background hiss (thermal noise).

This is exactly what ZF does mathematically. It creates a **spatial filter** that has **nulls** (zeros) in the directions of all interfering users, while maintaining a response toward the desired user.

> **Geometric interpretation**: In an MN-dimensional signal space, each user's channel occupies a "direction." ZF projects the received signal onto a direction that is **orthogonal to all other users' channels** but aligned with the desired user's channel. This is only possible when MN > K (more dimensions than users).

> **Source**: Wireless Pi — "Zero-Forcing (ZF) Detection in Massive MIMO Systems"
> https://wirelesspi.com/zero-forcing-zf-detection-in-massive-mimo-systems/

---

## 2.2 The ZF Detector Matrix

### Definition

When the ZF receiver is implemented at the CPU, the detection matrix is:

```
A = G_hat * (G_hat^H * G_hat)^(-1)
```

Where:
- **G_hat** = [g_hat_1, ..., g_hat_K] in C^(MN x K) — the **estimated** global channel matrix
- **g_hat_k** = [g_hat_1k^T, ..., g_hat_Mk^T]^T in C^(MN x 1) — stacked estimated channel from all APs to user k

This is the **Moore-Penrose pseudo-inverse** of G_hat.

### Why this specific formula?

The key property of the pseudo-inverse is:

```
A^H * G_hat = (G_hat^H * G_hat)^(-1) * G_hat^H * G_hat = I_K   (K x K identity matrix)
```

This means:

```
a_k^H * g_hat_i = delta_{ki} = { 1 if i = k
                                 { 0 if i != k
```

**In words**: The ZF combining vector for user k, when applied to the estimated channel of user i:
- Gives **1** if i = k (desired signal passes through)
- Gives **0** if i != k (interference from user i is **completely eliminated**)

This is the "zero forcing" property — it **forces** the contribution of all interfering estimated channels to **zero**.

### Requirement for ZF to exist

The matrix (G_hat^H * G_hat) must be **invertible**. This requires:
- G_hat has **full column rank** = K
- This needs **MN >= K** (total receive antennas >= number of users)
- The columns of G_hat must be **linearly independent** (which happens almost surely with random channels, UNLESS there is pilot contamination making channels parallel)

---

## 2.3 The Received Signal After ZF Processing (Equation 12)

After applying ZF, the detected signal for user k becomes:

```
r_k^ZF = sqrt(rho_u) * eta_k * s_k
       + sqrt(rho_u) * SUM_{i=1}^{K} sqrt(eta_i) * a_k^H * g_tilde_i * s_i
       + a_k^H * w_u     ... (12)
```

### Term 1: Desired Signal

```
sqrt(rho_u) * eta_k * s_k
```

This is the **clean desired signal** from user k. The ZF property (a_k^H * g_hat_k = 1) extracts the desired signal cleanly.

### Term 2: Residual Interference from Channel Estimation Errors

```
sqrt(rho_u) * SUM_{i=1}^{K} sqrt(eta_i) * a_k^H * g_tilde_i * s_i
```

Even though ZF perfectly cancels interference based on the **estimated** channels, it cannot cancel interference caused by **estimation errors**. The ZF detector doesn't know about g_tilde_i — it only knows g_hat_i.

**This term is why channel estimation quality is so important for ZF.**

### Term 3: Noise After ZF Processing

```
a_k^H * w_u
```

The variance of this term is ||a_k||^2 * sigma^2. The key issue: **||a_k||^2 can be large** — this is the **noise amplification problem** of ZF.

> **Why does ZF amplify noise?** To force interference to zero, the ZF vector a_k must be orthogonal to all other users' channel estimates. This constrains a_k to lie in a specific subspace. If this subspace happens to have a small projection onto g_hat_k, then ||a_k||^2 becomes large, which amplifies noise.

---

## 2.4 The SINR Expression (Equation 15)

The exact SINR for user k with ZF is:

```
SINR_k^ZF = rho_u * eta_k / [rho_u * SUM_m SUM_n |[a_mk]_n|^2 * SUM_i eta_i * (beta_mi - alpha_mi) + ||a_k||^2]     ... (15)
```

**Numerator**: `rho_u * eta_k` — desired signal power

**Denominator — First part**: Interference power from channel estimation errors

**Denominator — Second part**: `||a_k||^2` — noise amplification factor

**The problem with Equation 15**: It depends on the **random** vector a_k. Computing the rate requires Monte Carlo simulation. **This motivates Theorem 1.**

---

## 2.5 Theorem 1: The Tight Approximate Rate Expression (Equation 16)

This is the **main theoretical contribution** of the paper for ZF:

```
R_k^ZF ~ log2(1 + rho_u * Omega(k) * eta_k / [rho_u * SUM_{i=1}^{K} eta_i * c_i + 1])     ... (16)
```

### Component 1: c_i (Equation 17)

```
c_i = arg max_m (beta_mi - alpha_mi)     ... (17)
```

c_i is the **worst-case (largest) channel estimation error** for user i across all APs. By using the worst-case error, the rate approximation becomes a tight lower bound.

**When is c_i small (good)?**
- When channel estimation is accurate (alpha_mi ~ beta_mi for all APs)
- When there's no pilot contamination (tau >= K)
- When pilot SNR is high

### Component 2: Omega(k) (Equation 18)

```
Omega(k) = [SUM_{m in M_k} alpha_mk^2 / SUM_{m in M_k} alpha_mk]
         * [N * (SUM_{m in M_k} alpha_mk)^2 / (SUM_{m in M_k} alpha_mk^2) - 1]     ... (18)
```

Omega(k) represents the **effective array gain** that user k experiences from the ZF receiver.

**Factor 1**: `SUM alpha_mk^2 / SUM alpha_mk` — weighted average of channel estimation qualities, giving more weight to better APs.

**Factor 2**: `N * (SUM alpha_mk)^2 / (SUM alpha_mk^2) - 1` — relates to degrees of freedom available after ZF nulling. N (antennas per AP) plays a crucial role — more antennas = more spatial dimensions.

**Key insight about N**: When N increases, Omega(k) increases roughly linearly. More antennas per AP -> higher rate. This is why the paper's Fig. 2 shows dramatic improvement when N goes from 2 to 6.

### Component 3: M_k (Equation 19)

```
M_k = {all m != m_{j*} | j != k}     ... (19)
```

where:

```
m_{j*} = arg max_m beta_mj     ... (20)
```

M_k is the set of APs that effectively contribute to user k's ZF detection, excluding APs "dominated" by other users. For each user j != k, the AP with the strongest link to user j is excluded from M_k.

---

## 2.6 The Proof Sketch of Theorem 1

### Step 1: Upper-bound the estimation error (Eq. 47-48)

Replace (beta_mi - alpha_mi) with c_i = max_m(beta_mi - alpha_mi):

```
R_k^ZF >= E{log2(1 + rho_u * eta_k / [(rho_u * SUM_i eta_i * c_i + 1) * ||a_k||^2])}     ... (48)
```

### Step 2: Jensen's Inequality (Eq. 49)

Since -log2(1 + 1/x) is **convex** in x:

```
E{log2(1 + A/||a_k||^2)} >= log2(1 + A / E{||a_k||^2})     ... (49)
```

### Step 3: Handle pilot contamination (Eq. 50)

If users k and j share the same pilot (phi_k = phi_j):

```
g_hat_mk = (beta_mk / beta_mj) * g_hat_mj     ... (50)
```

Channel estimates become **proportional** (parallel vectors) -> G_hat is rank-deficient.

### Step 4: Approximate 1/||a_k||^2 (Eq. 51)

Using Wang & Dai approximation:

```
1/||a_k||^2 ~ SUM_{m in M_k} SUM_n |[g_hat_mk]_n|^2     ... (51)
```

### Step 5: Gamma distribution approximation (Eq. 54-56)

The sum follows a Gamma distribution. Taking the expectation gives Omega(k), yielding the final result (16).

---

## 2.7 Key Remarks and Insights

### Remark 1: Tightness of the Approximation

Equation (16) is very close to the genie-aided CSI case. Fig. 1 confirms the gap is marginal.

### Remark 2: Sensitivity to Channel Estimation

From Fig. 3: When tau drops from 10 to 8, the ZF receiver's 5%-outage rate drops by **208.4%**!

> **ZF is highly sensitive to channel estimation quality. If you use ZF, you MUST invest in good channel estimation.**

In contrast, CB is more robust to estimation errors.

> **Source**: Emil Bjornson's blog post "Pilot Contamination in a Nutshell"
> https://ma-mimo.ellintech.se/2017/01/14/pilot-contamination-in-a-nutshell/

### The Noise Amplification Problem

When MN is only slightly larger than K (tight system), ||a_k||^2 becomes large -> massive noise amplification.

When MN >> K (massive MIMO regime):
- a_k can be chosen to be short -> noise amplification becomes negligible
- ZF approaches MRC performance with interference cancellation

This is why **massive MIMO makes ZF practical**.

> **Source**: E. Bjornson, J. Hoydis, and L. Sanguinetti, "Massive MIMO Networks," 2017.
> https://massivemimobook.com

---

## 2.8 Power Control: Total Rate Maximization (Equations 21-29)

### The Optimization Problem (Eq. 21)

```
maximize    SUM_{k=1}^{K} R_k^ZF
subject to  R_k^ZF >= R_bar_k,  for all k       (QoS constraint)
            0 <= eta_k <= 1,    for all k       (power constraint)
```

Find power coefficients that **maximize total system throughput** while guaranteeing minimum quality for each user.

### Why is this problem hard?

The sum of log-ratio functions is **neither convex nor concave** in eta_k. Reference [14] proved this type of problem is **NP-hard**.

### The SCA (Sequential Convex Approximation) Method

**Key idea**: At each iteration n, replace the non-convex objective with a **convex lower bound** that is tight at the current solution.

**Algorithm 1:**

```
1. Choose initial feasible point {zeta_k^(0), t_k^(0)}, set tolerance epsilon
2. While not converged:
   a. Solve the convex subproblem (Eq. 29)
   b. Update: zeta_k^(n+1), t_k^(n+1) <- solution
   c. If |SUM(t_k^(n+1) - t_k^(n))| < epsilon, STOP
   d. n := n + 1
3. Return optimal power: eta_k* = (zeta_k*)^2
```

**Convergence**: Guaranteed (monotonically increasing, bounded above). Fig. 5 shows convergence in ~4 iterations.

**Complexity per iteration**: O((2K)^4.5 + (2K)^3.5)

---

## 2.9 Power Control: One User's Rate Maximization (Equations 30-31)

Maximize user 1's rate while ensuring all other users meet QoS. This converts to a **Geometric Program (GP)** — solvable globally by CVX.

**Results (Table 2)**: ZF User 1 achieves **7.86-9.88 bits/s/Hz**; all others get exactly **1.0 bits/s/Hz** (QoS threshold).

---

## 2.10 ZF: Practical Considerations and External Insights

### Centralized vs. Distributed ZF

This paper's ZF is **fully centralized**. Bjornson & Sanguinetti (2020) showed that **distributed MMSE** (L-MMSE) achieves nearly the same performance with much lower fronthaul.

> **Source**: E. Bjornson and L. Sanguinetti, "Making Cell-Free Massive MIMO Competitive With MMSE Processing," IEEE TWC, 2020.
> https://arxiv.org/abs/1903.10611

### Local Partial Zero-Forcing (PZF)

Interdonato et al. (2020) proposed a compromise: each AP applies ZF only to a subset of its strongest users and MRC for the rest.

> **Source**: G. Interdonato et al., "Local Partial Zero-Forcing Precoding for Cell-Free Massive MIMO," IEEE TWC, 2020.
> https://arxiv.org/abs/1909.01034

---

## 2.11 Summary Table: ZF Receiver

| Aspect | Detail |
|--------|--------|
| **Detector matrix** | A = G_hat * (G_hat^H * G_hat)^(-1) |
| **Key property** | a_k^H * g_hat_i = delta_{ki} (nulls all interference) |
| **SINR limited by** | Estimation errors + noise amplification |
| **Rate expression** | Eq. (16): depends only on beta_mk, alpha_mk |
| **Key parameters** | Omega(k) = effective array gain; c_i = worst-case error |
| **Power control** | SCA for sum rate; GP for single-user rate |
| **Strength** | Eliminates inter-user interference |
| **Weakness** | Noise amplification; sensitive to CSI errors; needs centralized processing |
| **Fronthaul** | High (raw signals to CPU) |
| **Complexity** | O(K^3) for matrix inversion at CPU |
| **Best regime** | High SNR, many APs, many antennas/AP, good CSI |

---

# 3. CB (Conjugate Beamforming) Receiver

## 3.1 What is Conjugate Beamforming? (Deep Intuition)

Conjugate Beamforming (CB) goes by **three equivalent names**:

| Name | Emphasis | Context |
|------|----------|---------|
| **Conjugate Beamforming (CB)** | The combining vector is the conjugate of the channel | Cell-free MIMO literature |
| **Matched Filter (MF)** | The filter is "matched" to the channel | Classic signal processing |
| **Maximum Ratio Combining (MRC)** | It maximizes the receive SNR | Traditional antenna combining |

### The Core Philosophy

- **ZF says**: "I will eliminate all interference, even if it costs me some noise amplification."
- **CB says**: "I will collect as much of my desired signal as possible, and I don't care about interference at all."

### Why is CB called "Maximum Ratio Combining"?

When there's **only one user** (no interference), CB is the **optimal linear detector**. It weights each antenna's contribution proportionally to its channel strength, and aligns all phases for coherent addition. This maximizes the output SNR.

> **Source**: David Tse and Pramod Viswanath, "Fundamentals of Wireless Communication," Cambridge, 2005.

> **Source**: Wireless Pi — https://wirelesspi.com/maximum-ratio-combining-mrc/

---

## 3.2 The CB Detector Matrix

The CB receiver simply sets:

```
A = G_hat
```

That's it. No matrix inversion. No pseudo-inverse. Just the estimated channel matrix itself.

### Computational Advantage

| Operation | CB | ZF |
|-----------|-----|-----|
| Matrix inversion? | **NO** | YES — O(K^3) |
| What each AP computes | g_hat_mk^H * y_m (local inner product) | Must send raw signal to CPU |
| Distributed? | **YES** — each AP works independently | **NO** — CPU needs global info |
| Fronthaul per AP | K scalar values (one per user) | N-dimensional signal vectors |

---

## 3.3 The Received Signal After CB Processing (Equation 32)

```
r_k^CB = sqrt(rho_u * eta_k) * g_hat_k^H * g_k * s_k
       + sqrt(rho_u) * SUM_{k' != k} sqrt(eta_{k'}) * g_hat_k^H * g_{k'} * s_{k'}
       + g_hat_k^H * w_u          ... (32)
```

### Term 1: Desired Signal

```
sqrt(rho_u * eta_k) * g_hat_k^H * g_k * s_k
```

The desired signal power grows as **(SUM_m alpha_mk)^2** — the contributions from all APs add **coherently**. More APs = quadratically more signal power.

### Term 2: Inter-User Interference (THE CRITICAL DIFFERENCE FROM ZF)

```
sqrt(rho_u) * SUM_{k' != k} sqrt(eta_{k'}) * g_hat_k^H * g_{k'} * s_{k'}
```

**This term does NOT vanish!** The CB receiver makes **no attempt** to suppress interference.

This interference has two components:

**(a) Non-coherent interference** (from users with different pilots):
- g_hat_k and g_{k'} are independent -> inner product has zero mean but non-zero variance
- Grows **linearly** with M

**(b) Coherent interference / Pilot contamination** (from users sharing same pilot):
- Inner product has **non-zero mean** -> adds coherently
- Grows **quadratically** with M
- Even with M -> infinity, this does NOT vanish relative to the desired signal

### Term 3: Noise

```
g_hat_k^H * w_u
```

Unlike ZF, CB does **NOT amplify** noise beyond the natural level ||g_hat_k||^2 * sigma^2.

---

## 3.4 The Achievable Rate for CB (Lemma 1, Equation 33)

```
R_k^CB = log2(1 + eta_k * ||l_kk||_2^2 /
         [SUM_{i!=k} eta_i * ||l_ik||_2^2
          + (1/N) * SUM_{i=1}^K eta_i * ||theta_ik||_2^2
          + (1/(N*rho_u)) * ||l_kk||_1])     ... (33)
```

### The l_ik vector (Equation 34)

```
l_ik = |phi_k^H * phi_i| * [alpha_1k * beta_1i/beta_1k, ..., alpha_Mk * beta_Mi/beta_Mk]^T     ... (34)
```

l_ik captures the **coherent interference** from user i to user k. It equals zero when pilots are orthogonal (|phi_k^H * phi_i| = 0).

**Special case — l_kk** (self-signal, i = k):

```
l_kk = [alpha_1k, alpha_2k, ..., alpha_Mk]^T
```

This is the vector of channel estimation qualities. **||l_kk||_2^2 = SUM_m alpha_mk^2** is the desired signal power.

### The theta_ik vector (Equation 35)

```
theta_ik = [sqrt(beta_1i * alpha_1k), ..., sqrt(beta_Mi * alpha_Mk)]^T     ... (35)
```

theta_ik captures the **non-coherent interference** from user i to user k — interference that exists regardless of pilot assignments.

**||theta_ik||_2^2 = SUM_m beta_mi * alpha_mk** grows linearly with M.

### The ||l_kk||_1 term (noise)

```
||l_kk||_1 = SUM_m alpha_mk
```

Noise contribution = (1/(N * rho_u)) * SUM_m alpha_mk. Grows linearly with M, becomes negligible as M -> infinity.

---

## 3.5 Dissecting the SINR of CB (Term by Term)

```
SINR_k^CB = [Desired Signal Power] / [Coherent Interf. + Non-coherent Interf. + Noise]
```

### Asymptotic behavior (M -> infinity)

- **Signal** ~ O(M^2)
- **Coherent interference** ~ O(M^2) (stays competitive with signal)
- **Non-coherent interference** ~ O(M) -> negligible
- **Noise** ~ O(M) -> negligible

If there is **NO pilot contamination** (tau >= K), the SINR grows without bound as M -> infinity.

> **Source**: H. Q. Ngo et al., "Cell-Free Massive MIMO Versus Small Cells," IEEE TWC, 2017.
> https://ieeexplore.ieee.org/document/7827017

---

## 3.6 Power Control for CB: Total Rate Maximization (Equations 36-43)

Same SCA framework as ZF (Algorithm 3). Non-convex problem handled by iterative convex approximation. Convergence in ~4 iterations. Complexity: O((2K)^4.5 + (2K)^3.5) per iteration.

---

## 3.7 One User's Rate Maximization for CB (Equations 44-45)

Same GP structure as ZF (Algorithm 4). Solvable by CVX.

**Results (Table 2)**: CB User 1 achieves **2.94-4.03 bits/s/Hz** — about **2.5x lower than ZF**.

---

## 3.8 CB: The Distributed Processing Advantage

### Layer 1: Local Processing at Each AP

Each AP m computes locally:

```
s_hat_mk = g_hat_mk^H * y_m    (for each user k = 1, ..., K)
```

Then sends K **scalar values** to the CPU.

### Layer 2: Combining at CPU

```
s_hat_k = SUM_{m=1}^M a_mk * s_hat_mk
```

### Fronthaul Requirement Comparison

| | CB (Distributed) | ZF (Centralized) |
|---|---|---|
| **Per AP, per coherence block** | K complex scalars | N x T complex values |
| **For M=60, K=10, N=6, T=200** | 60 x 10 = **600 scalars** | 60 x 6 x 200 = **72,000 values** |
| **Ratio** | **1x** | **120x** more |

> **Source**: O. T. Demir, E. Bjornson, L. Sanguinetti, "Foundations of User-Centric Cell-Free Massive MIMO," 2021.
> https://arxiv.org/abs/2108.02541
> GitHub: https://github.com/emilbjornson/cell-free-book

---

## 3.9 CB vs. ZF: The Complete Comparison

### Performance Comparison (from paper simulations)

| Metric | ZF | CB | Winner |
|--------|-----|-----|--------|
| Sum rate (M=60, K=10, N=6, EPC) | ~55 bits/s/Hz | ~30 bits/s/Hz | **ZF** by ~83% |
| Sum rate with power optimization | ~58 bits/s/Hz | ~33 bits/s/Hz | **ZF** by ~76% |
| Median per-user rate (N=6) | ~5.5 bits/s/Hz | ~3.0 bits/s/Hz | **ZF** by ~83% |
| 5%-outage rate (N=6) | ~2.5 bits/s/Hz | ~1.5 bits/s/Hz | **ZF** by ~67% |
| % users > 4 bits/s/Hz (N=6) | >52% | ~10% | **ZF** dominates |
| User 1 max rate | 7.86-9.88 | 2.94-4.03 | **ZF** by ~2.5x |
| Total power consumption | Higher | **Lower** | **CB** wins |
| Robustness to pilot contamination | Sensitive | **Robust** | **CB** wins |
| Low-SNR regime | Slightly worse | **Slightly better** | **CB** wins |

### Theoretical Comparison

| Property | ZF | CB |
|----------|-----|-----|
| Interference cancellation | Complete (for estimated channels) | None |
| Noise amplification | Yes (matrix inversion) | No |
| Processing location | Centralized (CPU) | Distributed (APs) |
| CSI requirement | Global G_hat at CPU | Local g_hat_mk at each AP |
| Computational complexity | O(K^3) + O(MNK) | O(MNK) only |
| Fronthaul load | High (raw signals) | Low (K scalars/AP) |
| Scalability | Poor (centralized bottleneck) | Excellent |
| Sensitivity to CSI errors | High | Low |
| Asymptotic limit (M -> inf) | Limited by estimation errors | Limited by pilot contamination |
| Degree-of-freedom requirement | MN > K necessary | Works with any M, N |

### When to Use Which?

**Use ZF when:**
- You have excellent channel estimation (tau >= K, high pilot SNR)
- You have sufficient backhaul/fronthaul capacity
- You need maximum spectral efficiency
- The system is interference-limited (many users, high SNR)
- You can afford centralized processing at the CPU

**Use CB when:**
- Fronthaul is limited (e.g., wireless fronthaul)
- You need a scalable, distributed solution
- Channel estimation is poor (few pilots, low pilot SNR)
- The system is noise-limited (few users, low SNR)
- You need low-complexity implementation

### The Modern View: Beyond ZF and CB

Bjornson and Sanguinetti (2020) showed that **MMSE combining** is the natural generalization that balances interference suppression and noise amplification:

```
A_MMSE = (G_hat * G_hat^H + (1/rho_u) * I)^(-1) * G_hat
```

- When rho_u -> infinity (high SNR): A_MMSE -> A_ZF
- When rho_u -> 0 (low SNR): A_MMSE -> G_hat = A_CB
- MMSE automatically adapts between these extremes

> **Source**: E. Bjornson and L. Sanguinetti, "Making Cell-Free Massive MIMO Competitive With MMSE Processing," IEEE TWC, 2020.
> https://arxiv.org/abs/1903.10611

---

## 3.10 The Big Picture: What This Paper Contributes

### Historical Timeline

| Year | Development | Key Reference |
|------|-------------|---------------|
| 2010 | Massive MIMO invented | Marzetta, IEEE TWC |
| 2015 | Cell-free massive MIMO concept | Ngo et al., IEEE Asilomar |
| 2017 | Cell-free vs. Small Cells (CB only) | Ngo et al., IEEE TWC |
| 2018 | Max-min fairness for CB | Bashar et al., IEEE ICC |
| **2019** | **This paper: ZF + CB with power control** | **Zhang et al., JCN** |
| 2020 | MMSE processing makes cell-free competitive | Bjornson & Sanguinetti, IEEE TWC |
| 2020 | Local partial ZF | Interdonato et al., IEEE TWC |
| 2021 | Comprehensive monograph | Demir, Bjornson, Sanguinetti |

### This Paper's Unique Contributions

1. **First closed-form ZF rate expression** for cell-free mMIMO with multi-antenna APs and pilot contamination (Theorem 1)
2. **SCA-based power control** for both ZF and CB — sum-rate maximization with QoS constraints
3. **GP-based single-user rate maximization** (Algorithms 2 and 4)
4. **Comprehensive ZF vs. CB comparison** with power optimization

---

# 4. Architecture Diagrams

## ZF vs CB Receiver Architecture

```
=====================================================================================
                        K SINGLE-ANTENNA USERS
         User 1 (s1,eta1)     User 2 (s2,eta2)  ...  User K (sK,etaK)
=====================================================================================
             |                   |                   |
             |    g_mk = sqrt(beta_mk) * h_mk  (Rayleigh fading channel)
             |                   |                   |
             v                   v                   v
=====================================================================================
              M DISTRIBUTED ACCESS POINTS  (N antennas each)

   +----------+        +----------+              +----------+
   |  AP 1    |        |  AP 2    |    . . .     |  AP M    |
   | (N ant.) |        | (N ant.) |              | (N ant.) |
   | y_1,u    |        | y_2,u    |              | y_M,u    |
   +----+-----+        +----+-----+              +----+-----+
=====================================================================================
         |                   |                        |
         |  MMSE Estimation: g_hat_mk = c_mk * Y_tilde_mk
         |  (done locally at each AP)
         |                   |                        |
    +----+-------------------+------------------------+----+
    |                                                       |
    v                                                       v
+===============================+  +=========================================+
|  CB / MRC  (DISTRIBUTED)      |  |   ZF  (CENTRALIZED)                     |
+===============================+  +=========================================+
|                               |  |                                         |
|  STEP 1: LOCAL at each AP     |  |  STEP 1: FORWARD to CPU                |
|  Compute inner product:       |  |  Send raw received signals y_m          |
|  s_hat_mk = g_hat_mk^H * y_m |  |  and channel estimates g_hat_mk         |
|  (for each user k)            |  |  to the CPU                             |
|  Complexity: O(NK)            |  |  Complexity: O(NK) per AP               |
|                               |  |                                         |
|  STEP 2: FRONTHAUL            |  |  STEP 2: FRONTHAUL                      |
|  Send K scalars per AP        |  |  Send N-dim vectors per AP              |
|  {s_hat_m1, ..., s_hat_mK}   |  |  {y_m, g_hat_m1,..., g_hat_mK}          |
|  >> TOTAL: M*K scalars        |  |  >> TOTAL: M*N*T values                 |
|  >> VERY LOW bandwidth        |  |  >> VERY HIGH bandwidth                 |
|                               |  |                                         |
|  STEP 3: CPU (simple)         |  |  STEP 3: CPU (heavy)                    |
|  Weighted combination:        |  |  a) Stack global matrix:                |
|  s_hat_k = SUM_m a_mk*s_hat_mk| |     G_hat = [g_hat_1,...,g_hat_K]       |
|  (just M additions per user)  |  |  b) Compute pseudo-inverse:             |
|  Complexity: O(MK)            |  |     A = G_hat*(G_hat^H*G_hat)^{-1}     |
|                               |  |     Complexity: O(K^3)                  |
|  DETECTED SIGNAL:             |  |  c) Apply ZF combining:                 |
|  r_k = desired signal         |  |     r_k = a_k^H * y_u                  |
|       + INTER-USER INTERF.    |  |     where a_k^H*g_hat_i = delta_{ki}   |
|       (NOT CANCELLED!)        |  |                                         |
|       + noise (normal)        |  |  DETECTED SIGNAL:                       |
|                               |  |  r_k = desired signal (CLEAN!)          |
|  RATE LIMITED BY:             |  |       + estimation error residual       |
|  * Pilot contamination        |  |       + AMPLIFIED noise                 |
|  * All inter-user interference|  |                                         |
|                               |  |  RATE LIMITED BY:                       |
|  RESULT: Lower rate           |  |  * Channel estimation errors            |
|  but ROBUST and SCALABLE      |  |  * Noise amplification                  |
+===============================+  |                                         |
                                   |  RESULT: Higher rate                     |
                                   |  but SENSITIVE to CSI                    |
                                   +=========================================+
```

## Side-by-Side Comparison

```
+----------------------+------------------------+-----------------------------+
|       PROPERTY       |    CB / MRC             |     ZF                      |
+----------------------+------------------------+-----------------------------+
| Detector matrix      | A = G_hat              | A = G_hat*(G_hat^H*G_hat)^-1|
| Key operation        | Matched filtering      | Pseudo-inverse              |
| Matrix inversion?    | NO                     | YES - O(K^3)                |
| Interf. cancelled?   | NO                     | YES (for estimated ch.)     |
| Noise amplification? | NO                     | YES                         |
| Processing location  | Distributed (at APs)   | Centralized (at CPU)        |
| CSI needed where?    | Local at each AP       | Global G_hat at CPU         |
| Fronthaul load       | LOW (K scalars/AP)     | HIGH (N vectors/AP)         |
| Scalability          | Excellent              | Poor                        |
| Best SNR regime      | Low SNR                | High SNR                    |
| Sum rate (M=60,N=6)  | ~30 bits/s/Hz          | ~55 bits/s/Hz               |
| Sensitivity to CSI   | Robust                 | Very sensitive               |
| Power consumption    | Lower                  | Higher                      |
+----------------------+------------------------+-----------------------------+
```

## The MMSE Middle Ground

```
    CB (MRC)  <-------------- MMSE ----------------> ZF

    A = G_hat        A = (G_hat*G_hat^H + 1/rho*I)^-1*G_hat        A = G_hat*(G_hat^H*G_hat)^-1

                              |
                     +--------+----------+
                     |                   |
                Low SNR (rho->0)    High SNR (rho->inf)
                MMSE -> CB           MMSE -> ZF
                     |                   |
                     +--- MMSE optimally-+
                          balances both

    <-- Less interference suppression    More interference suppression -->
    <-- Less noise amplification         More noise amplification ------->
    <-- Lower complexity                 Higher complexity --------------->
```

---

# 5. Recommended Further Learning

## Must-Read Papers (in order)

1. **Ngo et al. (2017)** — "Cell-Free Massive MIMO Versus Small Cells" — the foundation
   https://ieeexplore.ieee.org/document/7827017

2. **Bjornson & Sanguinetti (2020)** — "Making Cell-Free Massive MIMO Competitive"
   https://arxiv.org/abs/1903.10611

3. **Demir, Bjornson, Sanguinetti (2021)** — "Foundations of User-Centric Cell-Free Massive MIMO"
   Free PDF + MATLAB code: https://github.com/emilbjornson/cell-free-book

4. **Interdonato et al. (2020)** — "Local Partial Zero-Forcing Precoding for Cell-Free Massive MIMO"
   https://arxiv.org/abs/1909.01034

5. **Marzetta (2010)** — "Noncooperative Cellular Wireless with Unlimited Numbers of BS Antennas"
   https://ieeexplore.ieee.org/document/5595728

## YouTube / Video Lectures

- **Wireless Future Podcast** — Episode 13: "Distributed and Cell-Free Massive MIMO"
  https://www.youtube.com/@WirelessFuture

- **Emil Bjornson's Massive MIMO lectures**
  https://www.youtube.com/@emaborjeson

- **one6G Open Lecture 8** — Cell-Free Massive MIMO (June 2024)
  https://one6g.org/events/open-lecture-8-cell-free-massive-mimo/

## Blogs

- **Wireless Future Blog** — https://ma-mimo.ellintech.se/
  (dozens of posts on cell-free massive MIMO)

- **Wireless Pi** — https://wirelesspi.com/
  (excellent ZF/MRC tutorials)

## Simulation Code (MATLAB)

- https://github.com/emilbjornson/cell-free-book
- https://github.com/emilbjornson/competitive-cell-free
- https://github.com/emilbjornson/scalable-cell-free

## Key Researchers

| Researcher | Affiliation | Contribution |
|-----------|------------|--------------|
| Thomas L. Marzetta | NYU | Invented massive MIMO |
| Emil Bjornson | KTH, Sweden | Cell-free signal processing, scalability |
| Hien Quoc Ngo | Queen's University Belfast | Cell-free concept, original analysis |
| Erik G. Larsson | Linkoping University | Massive MIMO fundamentals |
| Luca Sanguinetti | University of Pisa | Cell-free monograph, scalability |
| Ozlem Tugfe Demir | Linkoping University | Cell-free monograph |
| Giovanni Interdonato | Ericsson/Linkoping | Partial ZF, distributed processing |

## Survey Papers

- "A Review on Cell-Free Massive MIMO Systems" — MDPI Electronics, 2023
  https://www.mdpi.com/2079-9292/12/4/1001

- "Cell-Free Massive MIMO for 6G Wireless Communication Networks" — arXiv, 2021
  https://arxiv.org/abs/2110.07309

---

> **Note**: This analysis was prepared based on the paper by Zhang et al. (JCN, 2019) supplemented with external research from IEEE, arXiv, academic blogs, and YouTube resources. All external sources are cited with URLs where available.

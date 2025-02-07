# Asian Option Pricing using Monte Carlo Methods

**Université Paris Dauphine - Master 1  
Monte Carlo Methods (2023-2024)**

## Project Overview

This project focuses on pricing an **Asian call option** using **Monte Carlo simulation** techniques. The payoff of the option depends on the arithmetic average of the asset price over a specified time period. Several variance reduction techniques are implemented to enhance the accuracy of the Monte Carlo estimates.

## Mathematical Model

The asset price \( S_t \) follows a **Geometric Brownian Motion (GBM)**, which is defined as:

$$
S_t = S_0 \exp\left(\left(r - \frac{\sigma^2}{2}\right)t + \sigma W_t\right),
$$

where:
- \( S_0 = 1 \) is the initial asset price,
- \( r = 0 \) is the risk-free interest rate,
- \( \sigma = 0.25 \) is the volatility of the asset,
- \( W_t \) is a standard Brownian motion.

The **Asian call option** payoff depends on the average price of the underlying asset over time and is given by:

$$
G = \max\left(\frac{1}{n} \sum_{i=1}^{n} S_{t_i} - K, 0\right),
$$

where:
- \( K = 1 \) is the strike price,
- \( n \) is the number of time steps,
- \( T = 1 \) is the maturity time.

The goal is to compute the expected payoff under the risk-neutral measure:

$$
E[G] = E\left[\max\left(\frac{1}{n} \sum_{i=1}^{n} S_{t_i} - K, 0\right)\right].
$$

## Monte Carlo Simulation Method

### 1. Basic Monte Carlo Simulation

The basic approach involves simulating multiple paths of the underlying asset \( S_t \) and calculating the average payoff across all simulations.

**Steps:**
1. Discretize the time interval \( [0, T] \) into \( n \) equal steps of size \( \Delta t = \frac{T}{n} \).
2. Simulate \( M \) independent paths of the GBM using the following discretized form:

$$
S_{t_{i+1}} = S_{t_i} \exp\left(\left(r - \frac{\sigma^2}{2}\right)\Delta t + \sigma \sqrt{\Delta t} Z_i\right),
$$

where \( Z_i \sim \mathcal{N}(0,1) \) are independent standard normal random variables.

3. For each path, compute the arithmetic average of the simulated asset prices:

$$
\bar{S} = \frac{1}{n} \sum_{i=1}^{n} S_{t_i}.
$$

4. Calculate the payoff \( G = \max(\bar{S} - K, 0) \) for each path.
5. Estimate the option price as the average of all payoffs:

$$
\hat{C}_{MC} = e^{-rT} \frac{1}{M} \sum_{j=1}^{M} G_j.
$$

---

### 2. Variance Reduction Techniques

To improve the accuracy and efficiency of the Monte Carlo estimator, we apply the following variance reduction techniques:

#### a) Antithetic Variates

For each standard normal variable \( Z_i \), we generate both \( Z_i \) and \( -Z_i \) to create two correlated paths. The average of the payoffs from these two paths reduces variance.

**Estimator:**

$$
\hat{C}_{AV} = e^{-rT} \frac{1}{2M} \sum_{j=1}^{M} \left(G_j + G_j^{(antithetic)}\right).
$$

#### b) Control Variates

We use a related option with a known analytical solution (e.g., a geometric Asian option) as a control variate to adjust our estimator.

**Control Variate Estimator:**

$$
\hat{C}_{CV} = \hat{C}_{MC} + \beta \left(C_{exact} - \hat{C}_{control}\right),
$$

where:
- \( C_{exact} \) is the known price of the control variate,
- \( \hat{C}_{control} \) is the Monte Carlo estimate of the control variate,
- \( \beta \) is the optimal coefficient computed as:

$$
\beta = \frac{\text{Cov}(G, G_{control})}{\text{Var}(G_{control})}.
$$

#### c) Importance Sampling

Importance sampling changes the probability measure to give more weight to important outcomes (e.g., in-the-money scenarios), reducing variance.

This is achieved by introducing a drift shift in the Brownian motion and correcting with a likelihood ratio.

---

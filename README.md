# RL Pair Trading with OU Process

Reinforcement Learning Pair Trading with Ornstein-Uhlenbeck process.

## Spread

Co-integrated assets follow the equation from a research by Avellaneda[^1]:

$$
\ln\left(\frac{P_t}{P_{t_0}}\right) = \alpha(t-t_0) + \beta \ln\left(\frac{Q_t}{Q_{t_0}}\right) + X_t
$$

With this formula, we calculate the spread $X_t$.

[^1]: Avellaneda, M., & Lee, J.-H. (2010). Statistical arbitrage in the US equities market. Quantitative Finance, 10(7), 761–782. [Link to paper](https://econpapers.repec.org/article/tafquantf/v_3a10_3ay_3a2010_3ai_3a7_3ap_3a761-782.htm)

## Moving Window

Since only the most recent market condition is considered for further analysis, we use the autocorrelation function (ACF) to determine the moving window size for $\mu$ and $\sigma$.

$$
\text{ACF}(k) = \frac{\text{Cov}(Y_t, Y_{t+k})}{\text{Var}(Y_t)}
$$

## Spread Estimation

The process $X_t$ follows the Ornstein-Uhlenbeck (OU) process:

$$
dX_t = \theta (\mu - X_t) \, dt + \sigma \, dW_t
$$

where:
* $\theta$ is the rate of mean reversion,
* $\mu$ is the long-term mean of the process,
* $\sigma$ is the instantaneous volatility,
* $W_t$ is a Wiener process (or standard Brownian motion).

By the definition of cointegration, the long-term mean of the spread $\mu$ should be 0. Therefore, we can simplify the formula as:

$$
dX_t = - \theta X_t \, dt + \sigma \, dW_t
$$

The solution to the OU process using Itô's lemma is:

$$
X_{t_1} = X_{t_0} e^{-\theta (t_1 - t_0)} + \mu \left(1 - e^{-\theta (t_1 - t_0)}\right) + \sigma \int_{t_0}^{t_1} e^{-\theta (t_1-t)} \, dW_t
$$

When estimating the spread, we can remove the randomness from our estimation:

$$
X_{t_1} \approx X_{t_0} e^{-\theta (t_1 - t_0)} + \mu \left(1 - e^{-\theta (t_1 - t_0)}\right)
$$

Solving for $\theta$:

$$
\theta = -\ln\left(\left|\frac{X_{t+1} - \mu}{X_{t} - \mu}\right|\right)
$$

## Predict the next spread

Use the theta estimation $\theta'$ to estimate the next price.

$$
X_{t_1} \approx X_{t_0} e^{-\theta' (t_1 - t_0)} + \mu \left(1 - e^{-\theta' (t_1 - t_0)}\right)
$$

Based on the estimation to make trading decisions.

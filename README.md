# monte-carlo-pricing-exotic-options

An Asian option is an option on the average price of the underlying asset. One particular application of Asian options is hedging exchange rate risk. Asian options are also popular in the energy OTC market and many commodity markets. Furthermore, because an Asian option is based on a price average, an attempt to manipulate the asset price just before expiration will normally have little effect or no effect on the option’s value. Asian options should therefore be of particular interest in markets for thinly traded assets.

<p align="center">
  <img src="https://user-images.githubusercontent.com/80944214/157110771-3b9129ad-90c6-4e20-8f50-17cac1c798e4.png" />
</p>




The graph demonstrates that the volatility in the average price value (red line) tends to be smoother and lower in comparison to the standard price path denoted by the blue line. The graph suggests that the average is less exposed to sudden crashes or rallies in stock price and over time is harder to manipulate than a single stock price. Therefore, the average price value captures more of the general movement (upward/downward trend) rather than the specific and sudden movements.

**The price of the Asian option should be lower compared with a similar standard option**

Since the payoff of an Asian option is based on an average price of the underlying asset, it is less volatile than the asset price itself, and the option on a lower volatility asset is worth less. This is because higher volatility means higher upside risk or higher downside risk.  In this case, the more the averages, the lower the price. The intuition is that the pricing fluctuations are averaged more frequently hence the volatility is reduced. The Figure above confirms this intuition. The Asian option should be less expensive in comparison to the similar standard option.


**Monte Carlo General Algorithm**

```python
def asian_option_mc(s0, k, r, dt, sig, m, n):
    # It is an arithmetic solution by using Monte Carlo method
    delta_t = dt / m  # length of time interval
    c = []
    p = []
    for i in range(0, n):
        s = [s0]
        for j in range(0, m):
            s.append(s[-1] * exp((r - 0.5 * sig ** 2) * delta_t + (sig * sqrt(delta_t) * random.gauss(0, 1))))

        avg = np.mean(s)
        c.append(max((avg - k), 0))
        p.append(max((k - avg), 0))

    c_value = np.mean(c) * exp(-r * dt)
    c_standard_error = np.std(c) / np.sqrt(n)

    p_value = np.mean(p) * exp(-r * dt)
    p_standard_error = np.std(p) / np.sqrt(n)

    return {'call_MC': c_value, 'standard error(c)': c_standard_error,  'lower_CI': c_value-1.96*c_standard_error,
            'upper_CI': c_value+1.96*c_standard_error, 'put_MC': p_value, 'standard error(p)': p_standard_error}
```

**Price of option by Monte Carlo simulation using 100 paths:**

```python
print(asian_option_mc(200, 220, 0.02, 1, 0.20, 365, 100))
```
The average of <img src="https://render.githubusercontent.com/render/math?math=C^{A}">  over the 100 simulated prices is $3.8042.
The 95% confidence interval for <img src="https://render.githubusercontent.com/render/math?math=C^{A}"> is [2.0496 to 5.5588]. 

**Price of option by Monte Carlo simulation using 100,000 paths:**

```python
print(asian_option_mc(200, 220, 0.02, 1, 0.20, 365, 100000))
```
The average of <img src="https://render.githubusercontent.com/render/math?math=C^{A}">  over the 100,000 simulated prices is $ 3.2505.
The 95% confidence interval for <img src="https://render.githubusercontent.com/render/math?math=C^{A}">
 is [3.1944 to 3.3066]. 

We can see that an extremely large number of simulation paths (100,000) allows us to achieve a reasonably small confidence interval. More specifically, we notice that the size of confidence interval reduces slowly. In effect, improving the accuracy by 1 (0.1) order requires approximately 100 times more simulation paths, leading to an explosive increase of computational time. The smaller confidence interval indicates a more robust (accurate) value estimate. The range between confidence intervals decreases by 3.397 ([5.5588 - 2.0496] - [3.3066 - 3.1944]). 


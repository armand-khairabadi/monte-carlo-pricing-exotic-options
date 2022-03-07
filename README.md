# monte-carlo-pricing-exotic-options
```math
e^i + 1 = 0
```


<p align="center">
  <img src="https://user-images.githubusercontent.com/80944214/157110677-ae37cf7d-9f84-4272-b7e1-536301766327.png" />
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/80944214/157110771-3b9129ad-90c6-4e20-8f50-17cac1c798e4.png" />
</p>



An Asian option is an option on the average price of the underlying asset. One particular application of Asian options is hedging exchange rate risk. Asian options are also popular in the energy OTC market and many commodity markets. Furthermore, because an Asian option is based on a price average, an attempt to manipulate the asset price just before expiration will normally have little effect or no effect on the option’s value. Asian options should therefore be of particular interest in markets for thinly traded assets.
Consider the case of an Asian call option with discrete arithmetic averaging. An option with maturity of T years and strike K has the payoff


The graph demonstrates that the volatility in the average price value (red line) tends to be smoother and lower in comparison to the standard price path denoted by the blue line. The graph suggests that the average is less exposed to sudden crashes or rallies in stock price and over time is harder to manipulate than a single stock price. Therefore, the average price value captures more of the general movement (upward/downward trend) rather than the specific and sudden movements.

**Do you think the price of the Asian option should be lower, equal, or higher compared with a similar standard option? Why?**

Since the payoff of an Asian option is based on an average price of the underlying asset, it is less volatile than the asset price itself, and the option on a lower volatility asset is worth less. This is because higher volatility means higher upside risk or higher downside risk.  In this case, the more the averages, the lower the price. The intuition is that the pricing fluctuations are averaged more frequently hence the volatility is reduced. The Figure above confirms this intuition. The Asian option should be less expensive in comparison to the similar standard option.


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

```python
print(asian_option_mc(200, 220, 0.02, 1, 0.20, 365, 100))
```
The average of C_0^A  over the 100 simulated prices is $3.8042.
The 95% confidence interval for C_0^A is (3.8042-1.96*0.8952) to (3.8042+1.96*0.8952) or [2.0496 to 5.5588]. 

**Now with 100,000 paths:**
The average of C_0^A  over the 100,000 simulated prices is $ 3.2505.
The 95% confidence interval for C_0^A is (3.2505-1.96*0.0286) to (3.2505+1.96*0.0286) or [3.1944 to 3.3066]. 

```python
print(asian_option_mc(200, 220, 0.02, 1, 0.20, 365, 100000))
```

We can see that an extremely large number of simulation paths (100,000) allows us to achieve a reasonably small confidence interval. More specifically, we notice that the size of confidence interval reduces slowly. In effect, improving the accuracy by 1 (0.1) order requires approximately 100 times more simulation paths, leading to an explosive increase of computational time. The smaller confidence interval indicates a more robust (accurate) value estimate. The range between confidence intervals decreases by 3.397 ([5.5588 - 2.0496] - [3.3066 - 3.1944]). 


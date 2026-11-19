# DSP Code Reference

### Slide 7

```python
# noise variance over N residual samples
sigma2 = sum((y[n] - mu) ** 2 for n in range(N)) / (N - 1)
print(f"σ² ≈ {sigma2:.4f}")          # variance in V²
if sigma2 > tau2:                    # τ² alarm threshold
    alarm = True
```

Here we're taking the unbiased sample variance of the residual after removing the local mean — the N − 1 denominator matters for small windows — and the alarm only fires when that variance clears τ².

### Slide 9

```python
# transfer function on the unit circle z → e^{jω}
def H(z, omega):
    z = exp(1j * omega)                  # ω ∈ [−π, π]
    return (b0 + b1 * z**-1) / (1 + a1 * z**-1)

gain_at_w0 = abs(H(z, omega_0))         # ω₀ = π/4 rad/sample
```

This walks the transfer function around the unit circle: z = e^{jω} turns H(z) into the complex frequency response, and the absolute value at ω₀ gives the gain a spectrum analyser would read at that bin.

### Slide 11

```python
# leaky integrator: y[n] = α·x[n] + (1 − α)·y[n − 1]
alpha = 1 - exp(-1 / (tau_s * fs))       # α ∈ (0, 1]
y_now = alpha * x[n] + (1 - alpha) * y_prev
```

A one-pole low-pass smoother: the coefficient comes from the sample rate and the time constant you want, and each new sample blends into the running state, with α near zero meaning heavy smoothing.

### Slide 13

```python
# spectral centroid: Σ_k f_k·P_k / Σ_k P_k
weighted = sum(f[k] * P[k] for k in bins)
total    = sum(P[k] for k in bins)        # Σ_k P_k ≥ 0
centroid = weighted / total              # bins → Hz
```

This gives the power-weighted average bin frequency, i.e. where the spectrum sits on the frequency axis; keep an eye on the total power so a silent, all-zero spectrum does not turn into a division by zero.

### Slide 15

```python
# cross-correlation lag: peak of r_xy(τ) ⇒ delay
lags = range(-max_lag, max_lag + 1)
peak = argmax([r_xy(tau) for tau in lags])
delay_s = peak * 1000 / fs               # θ error ≤ 0.5 sample
```

Here the integer lag that maximises the cross-correlation is taken as the delay in samples and converted to milliseconds; the search is symmetric around zero, and the estimate is only resolved to about half a sample.

### Slide 17

```python
# Kalman gain: K = P⁻Hᵀ(H P⁻Hᵀ + R)⁻¹
S  = H @ P_minus @ H.T + R
K  = P_minus @ H.T @ inv(S)
x_hat = x_pred + K @ (z − H @ x_pred)    # x̂ ← x̂⁻ + K·(z − H x̂⁻)
```

One measurement update of a Kalman filter: build the innovation covariance from the predicted covariance and measurement noise, form the gain, then correct the predicted state with the innovation weighted by K.

### Slide 19

```python
# log-magnitude spectrum in dB: S_db(ω) = 20·log10|S(ω)|
for i, w in enumerate(omega_grid):
    S_db[i] = 20 * log10(abs(S(w))) + eps    # eps = 1e−12, ω → rad/s
```

This turns the complex magnitude spectrum into decibel levels with the factor-twenty convention; the tiny epsilon is there to keep the logarithm finite, and the frequency axis stays in rad/s.

# DSP Code Reference

### Slide 7

```python
# noise variance over N residual samples
sigma2 = sum((y[n] - mu) ** 2 for n in range(N)) / (N - 1)
print(f"σ² ≈ {sigma2:.4f}")          # variance in V²
if sigma2 > tau2:                    # τ² alarm threshold
    alarm = True
```

The snippet estimates the residual noise variance: the mean is removed first, the sum of squares is divided by N − 1 so the estimate is unbiased, and the result is compared with a squared alarm threshold.

### Slide 9

```python
# transfer function on the unit circle z → e^{jω}
def H(z, omega):
    z = exp(1j * omega)                  # ω ∈ [−π, π]
    return (b0 + b1 * z**-1) / (1 + a1 * z**-1)

gain_at_w0 = abs(H(z, omega_0))         # ω₀ = π/4 rad/sample
```

Evaluation of the transfer function around the unit circle gives the complex frequency response; substituting one angular frequency yields the gain at that single point of the sweep.

### Slide 11

```python
# leaky integrator: y[n] = α·x[n] + (1 − α)·y[n − 1]
alpha = 1 - exp(-1 / (tau_s * fs))       # α ∈ (0, 1]
y_now = alpha * x[n] + (1 - alpha) * y_prev
```

A first-order low-pass recursion whose coefficient follows from the sampling interval and the chosen time constant; the state update mixes the new sample with the previous output.

### Slide 13

```python
# spectral centroid: Σ_k f_k·P_k / Σ_k P_k
weighted = sum(f[k] * P[k] for k in bins)
total    = sum(P[k] for k in bins)        # Σ_k P_k ≥ 0
centroid = weighted / total              # bins → Hz
```

The spectral centroid is the power-weighted mean bin frequency; dividing the weighted sum by the total power makes the value independent of the absolute signal level.

### Slide 15

```python
# cross-correlation lag: peak of r_xy(τ) ⇒ delay
lags = range(-max_lag, max_lag + 1)
peak = argmax([r_xy(tau) for tau in lags])
delay_s = peak * 1000 / fs               # θ error ≤ 0.5 sample
```

The inter-channel delay is the lag at which the cross-correlation peaks; the peak index is converted to seconds, and the angular error stays below half a sample.

### Slide 17

```python
# Kalman gain: K = P⁻Hᵀ(H P⁻Hᵀ + R)⁻¹
S  = H @ P_minus @ H.T + R
K  = P_minus @ H.T @ inv(S)
x_hat = x_pred + K @ (z − H @ x_pred)    # x̂ ← x̂⁻ + K·(z − H x̂⁻)
```

One Kalman gain step: the innovation covariance is formed first, the gain weights predicted covariance against measurement noise, and the state is corrected with the measurement residual.

### Slide 19

```python
# log-magnitude spectrum in dB: S_db(ω) = 20·log10|S(ω)|
for i, w in enumerate(omega_grid):
    S_db[i] = 20 * log10(abs(S(w))) + eps    # eps = 1e−12, ω → rad/s
```

The magnitude spectrum is reported in decibels using the factor twenty; the small epsilon keeps the logarithm finite when the magnitude is close to zero.

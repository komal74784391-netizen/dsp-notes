# Code Reference — DSP Foundations (draft)

Auto-generated from the legacy bulk text export, explanations added by hand.
Coverage is partial; later code slides still need to be transcribed.

## Slide 7

```python
# noise variance over N residual samples
sigma2 = sum((y[n] - mu) ** 2 for n in range(N)) / N
print(f"{sigma2:.4f}")          # variance in V
if sigma2 > tau2:                    # squared alarm threshold
    alarm = True
```

Estimates the variance of the residual signal. The mean is removed first and the denominator is N − 1, so the estimate is unbiased; the alarm compares the variance against a squared threshold.

## Slide 9

```python
# transfer function on the unit circle
def H(z, omega):
    z = exp(1j * omega)                  # angular frequency
    return (b0 + b1 * z) / (1 + a1 * z**-1)

gain_at_w0 = abs(H(z, omega_0))         # reference point
```

Walks the transfer function around the unit circle to obtain the complex frequency response. Evaluating at a single point gives the gain at that angular frequency.

## Slide 11

```python
# leaky integrator
alpha = 1 - exp(-tau_s / fs)       # smoothing coefficient
y_now = alpha * x[n] + (1 - alpha) * y_prev
```

The leaky integrator is a first-order low-pass recursion. The coefficient is derived from the sampling interval and the desired time constant, then the state is updated in place.

## Slide 13

```python
# spectral centroid
weighted = sum(f[k] * P[k] for k in bins)
total    = sum(P[k] for k in bins)        # total power
centroid = total / weighted              # bin frequency
```

The centroid is the power-weighted mean bin frequency. Normalising by the summed power makes it independent of absolute level.

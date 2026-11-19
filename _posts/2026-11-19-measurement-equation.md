---
layout: post
title: "Starting with the Measurement Equation: The Forward Problem in Radio Interferometry"
date: 2026-11-19
excerpt: "Before any reconstruction algorithm can be evaluated, the forward problem must be precisely specified. This note derives the Radio Interferometer Measurement Equation from first principles, discusses the Van Cittert–Zernike theorem, and shows how to simulate sparse UV sampling and the resulting dirty image using Python and NumPy."
---

## Overview

A reconstruction algorithm — whether it is Högbom CLEAN or a data-driven U-Net — cannot be meaningfully evaluated without a precise specification of the *forward problem*: the mathematical model that maps the true sky brightness to the observed data. This note works through that derivation from first principles, as it underpins every subsequent experimental decision.

---

## 1. The Physical Measurement: Correlating Two Antennas

Consider two antennas *p* and *q* separated by a physical baseline vector **d** (metres). Each antenna collects electromagnetic radiation from the sky and converts it to a voltage signal. After downconversion, the two voltage streams are **correlated** — multiplied and time-averaged — to produce a **visibility**:

$$V_{pq} = \langle E_p(\nu, t) \cdot E_q^*(\nu, t) \rangle_{\Delta t}$$

where $E_p$ is the complex electric field amplitude at antenna $p$, $\nu$ is frequency, and $\langle \cdot \rangle_{\Delta t}$ denotes time-averaging over the correlator integration time $\Delta t$ (typically 1–30 seconds).

The visibility $V_{pq}$ is a **complex number** encoding both amplitude (how much correlated power was received) and phase (the path-length difference between the two antennas for emission from a given direction).

---

## 2. The Van Cittert–Zernike Theorem

For a spatially incoherent, partially-coherent extended source (a reasonable model for most astrophysical targets), the **Van Cittert–Zernike theorem** states that the mutual coherence function of the received field is proportional to the Fourier transform of the source brightness distribution. In the context of a radio interferometer, with the baseline expressed in wavelengths as:

$$\mathbf{b} = (u, v, w) \equiv \frac{\mathbf{d}}{\lambda}$$

the theorem gives, in the direction cosine coordinate system $(l, m, n)$ where $n = \sqrt{1 - l^2 - m^2}$:

$$V(u,v,w) = \iint \frac{A(l,m)\, I(l,m)}{n} \exp\!\left[-2\pi i \left(ul + vm + w(n-1)\right)\right] \frac{dl\, dm}{n}$$

This is the **Radio Interferometer Measurement Equation (RIME)** for a single baseline, single polarisation, narrow-field observation.

**Key terms:**
- $I(l, m)$ — sky brightness distribution (Jy/sr) at direction $(l,m)$ relative to the phase centre
- $A(l, m)$ — primary beam power pattern (dimensionless, $\leq 1$; describes the antenna's directional sensitivity)
- $w(n-1)$ — the **w-term**: a non-linear phase term arising from non-coplanar baselines; negligible for small fields-of-view but critical for LOFAR/MWA-class wide-field imaging

---

## 3. The Small-Field Approximation and the 2D Fourier Transform

When the field-of-view is small ($l, m \ll 1$, so $n \approx 1$ and $w(n-1) \approx 0$) and the primary beam $A$ can be treated as constant over the field, the RIME simplifies to a 2D Fourier transform:

$$\boxed{V(u,v) = \mathcal{F}\{I\}(u,v) = \iint I(l,m)\, e^{-2\pi i(ul + vm)}\, dl\, dm}$$

This is the fundamental operating principle of a synthesis radio telescope: **each baseline samples one complex Fourier coefficient of the sky brightness distribution**. The inverse relationship is:

$$I(l,m) = \iint V(u,v)\, e^{+2\pi i(ul + vm)}\, du\, dv$$

If $V(u,v)$ were known for all $(u,v)$, a simple inverse Fourier transform would recover $I(l,m)$ exactly. The core challenge is that only finitely many $(u,v)$ points are sampled.

---

## 4. Sampling, the Dirty Beam, and the Dirty Image

Let $S(u,v)$ be the sampling function — a sum of Dirac deltas at each observed visibility $(u_k, v_k)$, weighted by visibility weight $w_k$:

$$S(u,v) = \sum_k w_k\, \delta(u - u_k,\, v - v_k)$$

The measured data is $V_{\rm obs}(u,v) = S(u,v) \cdot V(u,v)$. Applying an inverse Fourier transform gives the **dirty image**:

$$I^D(l,m) = \mathcal{F}^{-1}\{S \cdot V\} = \mathcal{F}^{-1}\{S\} \star I = B^D \star I + N$$

where $\star$ denotes convolution, $B^D = \mathcal{F}^{-1}\{S\}$ is the **dirty beam (PSF)**, and $N$ is image-plane noise. Because $S$ is sparse (many UV points are unsampled), $B^D$ has strong sidelobes — the fundamental artefact in aperture synthesis imaging.

The angular resolution of the array is set by the longest baseline $B_{\rm max}$:

$$\theta_{\rm res} \approx \frac{\lambda}{B_{\rm max}}$$

---

## 5. Python Simulation

The following script simulates the full forward model — from a sky brightness map to a dirty image — using NumPy's FFT routines. This can be used to generate synthetic training data for the U-Net.

```python
import numpy as np
from pathlib import Path

def forward_model(sky: np.ndarray,
                  n_baselines: int = 2000,
                  noise_sigma: float = 0.01,
                  seed: int = 42) -> dict:
    """
    Simulate radio interferometric forward model.

    Parameters
    ----------
    sky        : (H, W) float ndarray — true sky brightness (Jy/pixel).
    n_baselines: number of unique baselines (UV samples).
    noise_sigma: thermal noise level in Jy (added to visibilities).
    seed       : reproducibility.

    Returns
    -------
    dict with keys: 'dirty', 'psf', 'uv_mask', 'visibilities_obs'
    """
    rng = np.random.default_rng(seed)
    H, W = sky.shape

    # --- Step 1: True visibilities (full 2D FFT) ---
    V_true = np.fft.fftshift(np.fft.fft2(np.fft.ifftshift(sky)))

    # --- Step 2: Build UV mask (simulate sparse baselines) ---
    # Sample random UV points with Hermitian symmetry (V(-u,-v) = V*(u,v))
    uv_mask = np.zeros((H, W), dtype=bool)
    u_idx = rng.integers(-H//2, H//2, size=n_baselines) % H
    v_idx = rng.integers(-W//2, W//2, size=n_baselines) % W
    for u, v in zip(u_idx, v_idx):
        uv_mask[u, v] = True
        uv_mask[-u % H, -v % W] = True   # enforce V(-u,-v) = V*(u,v)

    # --- Step 3: Apply sampling + add thermal noise ---
    noise = (rng.standard_normal((H, W)) + 1j * rng.standard_normal((H, W))) * noise_sigma / np.sqrt(2)
    V_obs = uv_mask * (V_true + noise)

    # --- Step 4: Dirty image = iFFT of sampled visibilities ---
    dirty = np.real(np.fft.fftshift(np.fft.ifft2(np.fft.ifftshift(V_obs))))

    # --- Step 5: PSF (dirty beam) = iFFT of sampling function ---
    V_psf = uv_mask.astype(complex)   # unit amplitude at all sampled points
    psf = np.real(np.fft.fftshift(np.fft.ifft2(np.fft.ifftshift(V_psf))))
    psf /= psf.max()  # normalise peak of PSF to 1

    return {
        "dirty":           dirty,
        "psf":             psf,
        "uv_mask":         uv_mask,
        "visibilities_obs": V_obs,
        "uv_coverage_pct": 100 * uv_mask.sum() / uv_mask.size,
    }


# Example usage
if __name__ == "__main__":
    import matplotlib.pyplot as plt
    from astropy.io import fits   # pip install astropy

    # Load a sky model (or create a synthetic Gaussian source)
    sky = np.zeros((256, 256))
    for _ in range(20):   # scatter 20 point sources
        rng = np.random.default_rng()
        x, y = rng.integers(30, 220, 2)
        sky[x, y] += rng.uniform(0.1, 1.0)

    result = forward_model(sky, n_baselines=3000, noise_sigma=0.005)
    print(f"UV coverage: {result['uv_coverage_pct']:.1f}%")

    fig, axes = plt.subplots(1, 3, figsize=(14, 4))
    axes[0].imshow(sky, cmap='inferno', origin='lower');   axes[0].set_title('True Sky')
    axes[1].imshow(result['psf'], cmap='RdBu_r', origin='lower'); axes[1].set_title('Dirty Beam (PSF)')
    axes[2].imshow(result['dirty'], cmap='inferno', origin='lower'); axes[2].set_title('Dirty Image')
    plt.tight_layout()
    plt.savefig('forward_model_demo.png', dpi=150, bbox_inches='tight')
```

---

## 6. What This Means for the Comparison

Both Högbom CLEAN and the U-Net receive the **same dirty image** as input and are evaluated against the **same reference sky**. The key difference is:

| Property | Högbom CLEAN | U-Net |
|---|---|---|
| Uses PSF explicitly? | Yes — subtracts PSF at each iteration | No — learned implicitly from training data |
| Needs training data? | No | Yes — requires (dirty, reference) pairs |
| Generalises to new UV coverage? | Yes — only uses S(u,v) | Unclear — requires testing |
| Runtime (inference) | O(N<sub>iter</sub> × N<sub>pix</sub>) | O(1) forward pass through network |
| Flux conservation guarantee | Theoretically yes (restoration step) | No formal guarantee; must be measured |

The next note will document the dataset selection decision and the train/validation/test split protocol.

---

*This note describes the planned methodology. No dataset has been selected and no network has been trained at this stage.*

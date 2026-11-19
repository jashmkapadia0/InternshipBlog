---
layout: default
title: Methodology
description: Full theoretical and implementation methodology for comparing Högbom CLEAN and U-Net radio interferometric image reconstruction, including data pipeline, training protocol, and evaluation framework.
permalink: /methodology/
---
<section class="page-hero"><p class="eyebrow">02 / METHOD</p><h1>Making the comparison reproducible.</h1><p class="lede">Every implementation choice — data provenance, forward model, training configuration, evaluation metric — must be fixed and documented before results are reported. This page records those choices as the project develops.</p></section>

<section class="section prose-section">

<figure class="article-figure">
  <img src="{{ '/assets/images/workflow.svg' | relative_url }}" alt="Planned workflow from sky model, UV sampling, visibilities, and dirty image to CLEAN or U-Net reconstruction and metrics" />
  <figcaption><span>FIG. 05</span> Project workflow schematic: sky model → UV sampling → visibilities → dirty image → CLEAN / U-Net → common evaluation. This is a planned pipeline; stages are not yet complete.</figcaption>
</figure>

<h2>1. Data Provenance and Forward Model</h2>

<p>The foundation of a valid comparison is a shared, well-documented forward model. The forward problem for a single-polarisation, narrow-field observation is:</p>

<div class="equation">V<sub>obs</sub>(u,v) = ∫∫ A(l,m) I<sub>true</sub>(l,m) e<sup>−2πi(ul+vm)</sup> dl dm + η(u,v)</div>

<p class="small-note">where η(u,v) ~ 𝒞𝒩(0, σ²) is complex thermal noise with variance σ² = (k<sub>B</sub> T<sub>sys</sub>)² / (2 Δν τ A<sub>eff</sub>²). Both CLEAN and the U-Net will receive exactly the same set of observed visibilities for each test case.</p>

<p>Dataset options under consideration (to be finalised and documented):</p>
<ul>
  <li><strong>Simulated sky + VLA-like UV coverage</strong> — sky maps generated from observed NVSS/FIRST source catalogues; UV coverage drawn from a standard 8-hour VLA A-configuration observation track.</li>
  <li><strong>MeerKAT GLEAM-X cross-matched simulations</strong> — Gaussian source components from GLEAM-X catalogue (Hurley-Walker et al.) convolved with a MeerKAT primary beam model.</li>
  <li><strong>SKAO Science Data Challenge data</strong> — the SDC1/SDC2 datasets provide realistic SKA-MID or SKA-LOW simulated sky cubes and may be used for benchmarking.</li>
</ul>

<h2>2. UV Sampling and Imaging Parameters</h2>

<p>All imaging parameters will be fixed and recorded before any experiment is run:</p>

<pre><code class="language-python">imaging_config = {
    # Array and observation
    "array":          "VLA-A",         # or MeerKAT
    "frequency_GHz":  1.4,             # L-band
    "bandwidth_MHz":  256,
    "integration_s":  10,
    "obs_duration_h": 8,
    "declination_deg": -30.0,

    # Imaging
    "image_size_px":  512,             # 512 × 512 pixels
    "cell_arcsec":    0.5,             # pixel scale (Nyquist-sampled at θ_res)
    "weighting":      "briggs",
    "robust":         0.0,             # Briggs robust parameter

    # CLEAN
    "clean_gain":     0.1,             # loop gain γ
    "clean_threshold_sigma": 3.0,      # stop at 3σ noise
    "clean_algorithm": "multiscale",   # Cornwell 2008
    "scales_arcsec":  [0, 5, 15, 30],  # multi-scale CLEAN scales

    # U-Net
    "unet_input_norm": "to_be_defined",  # preserve signed values; record transform
    "unet_target_norm": "to_be_defined", # record target transform and inverse
    "train_val_test_split": [0.7, 0.15, 0.15],
}
</code></pre>

<p class="callout"><strong>Normalization remains an open implementation choice.</strong> Per-image min–max scaling to [0,1] is not specified: it removes sign and can make the mapping between model output and physical flux ambiguous. Choose and document a transform that supports the signed input and target values in the selected dataset, fit any scale using training data only, and apply its inverse before physical-unit evaluation. The exact transform depends on the dataset and has not been selected.</p>

<h2>3. CLEAN Implementation Protocol</h2>

<p>CLEAN will be run using <strong>CASA</strong> (Common Astronomy Software Applications, McMullin et al. 2007) or <strong>WSClean</strong> (Offringa et al. 2014), both of which implement Multi-Scale CLEAN and W-projection. The key parameters to record:</p>

<ul>
  <li><strong>Implementation:</strong> CASA tclean / WSClean (version number recorded)</li>
  <li><strong>Algorithm:</strong> Multi-Scale CLEAN (ms-clean) with scales [0, 5, 15, 30] arcsec</li>
  <li><strong>Loop gain:</strong> γ = 0.1 per minor cycle</li>
  <li><strong>Stopping threshold:</strong> τ = 3σ, where σ is the theoretical thermal noise floor</li>
  <li><strong>Clean mask:</strong> No a priori mask (blind deconvolution) unless noted</li>
  <li><strong>Restoring beam:</strong> Gaussian fit to central lobe of dirty beam (FWHM documented per run)</li>
  <li><strong>Hardware:</strong> CPU-only run on documented machine; timing measured with Python <code>time.perf_counter()</code></li>
</ul>

<h2>4. U-Net Training Protocol</h2>

<p>The U-Net training pipeline will be fully specified before any training is started. The training framework uses PyTorch (version ≥ 2.0) with the following protocol:</p>

<pre><code class="language-python">training_config = {
    # Architecture
    "model":          "RadioUNet",
    "features":       [64, 128, 256, 512],  # channel widths
    "dropout":        0.0,                   # no dropout (small dataset)

    # Training
    "optimizer":      "AdamW",
    "lr":             1e-4,
    "lr_scheduler":   "CosineAnnealingLR",
    "epochs":         150,
    "batch_size":     8,
    "loss":           "0.7*MSE + 0.3*(1-SSIM)",

    # Augmentation (geometry-preserving only)
    "augmentation": {
        "horizontal_flip": True,     # flux-preserving
        "vertical_flip":   True,
        "rotation_90":     True,
        "brightness_jitter": False,  # would alter flux — excluded
    },

    # Regularisation
    "weight_decay":   1e-5,
    "early_stopping_patience": 20,   # stop if val loss does not improve

    # Reproducibility
    "seed":           42,
    "deterministic":  True,          # torch.use_deterministic_algorithms(True)
}
</code></pre>

<p><strong>Data leakage prevention</strong> is critical: the train/validation/test split is performed at the <em>observation</em> level (not at the patch level), so that no UV track or sky patch from a test case appears in training or validation.</p>

<h2>5. Fair-Comparison Principles</h2>

<ul>
  <li>Both CLEAN and U-Net receive the same set of test visibilities and the same reference image for each evaluation case.</li>
  <li>The same pixel grid, field of view, and flux scale are used for all comparisons.</li>
  <li>CLEAN's restoring beam is documented; U-Net output is at the same resolution without any post-convolution unless noted.</li>
  <li>Runtime is measured separately for CLEAN (imaging step only), U-Net training, and U-Net inference. These are not combined.</li>
  <li>Failed or inconclusive experiments are recorded explicitly; negative results are scientifically meaningful.</li>
  <li>All preprocessing and postprocessing steps (normalisation, noise injection, unit conversions) that affect flux or angular scale are documented and applied identically to both methods.</li>
</ul>

<h2>6. Evaluation Metrics</h2>

<p>Metric definitions are fixed below and will not be changed after experiments begin. Values remain blank until measurements are made on held-out test data.</p>

<div class="table-scroll"><table><thead><tr><th>Measure</th><th>Definition</th><th>Interpretation</th><th>CLEAN</th><th>U-Net</th></tr></thead><tbody>
<tr><th>RMSE</th><td>√(1/N · Σ (Î<sub>i</sub> − I<sub>ref,i</sub>)²) in Jy/beam</td><td>Lower = more accurate pixel intensities. Sensitive to outliers and flux scale.</td><td>—</td><td>—</td></tr>
<tr><th>PSNR</th><td>10 log₁₀(I<sub>max</sub>² / MSE) in dB</td><td>Higher = better. Defined w.r.t. declared peak value I<sub>max</sub>; must be reported.</td><td>—</td><td>—</td></tr>
<tr><th>SSIM</th><td>Structural similarity index ∈ [−1,1] (Wang et al. 2004)</td><td>Higher = more similar local structure. Window size and data range reported.</td><td>—</td><td>—</td></tr>
<tr><th>Flux recovery</th><td>F<sub>rec</sub> / F<sub>ref</sub>, where F = Σ I(l,m) above 3σ mask</td><td>Ratio = 1.0 is perfect. Mask and threshold documented. Sensitive to beam normalisation.</td><td>—</td><td>—</td></tr>
<tr><th>Runtime</th><td>Wall-clock time (s) for imaging (CLEAN) or inference (U-Net); training time separate</td><td>CLEAN = per-image. U-Net = training once + inference per image. Report both.</td><td>—</td><td>—</td></tr>
</tbody></table></div>

<pre><code class="language-python">import numpy as np
from skimage.metrics import structural_similarity as ssim_sk
from skimage.metrics import peak_signal_noise_ratio as psnr_sk

def evaluate_reconstruction(pred: np.ndarray, reference: np.ndarray,
                             mask: np.ndarray | None = None,
                             noise_sigma: float | None = None) -> dict:
    """
    Compute RMSE, PSNR, SSIM, and flux recovery for a single test case.

    Parameters
    ----------
    pred      : (H, W) reconstructed image (Jy/beam, normalised).
    reference : (H, W) reference/true sky (same units, same normalisation).
    mask      : (H, W) bool — region to evaluate (e.g., source region above 3σ).
    noise_sigma : estimated background noise level (for flux mask threshold).
    """
    if mask is None and noise_sigma is not None:
        mask = reference > 3 * noise_sigma  # 3σ threshold mask
    if mask is None:
        mask = np.ones_like(reference, dtype=bool)

    # Pixel-wise metrics on full image
    rmse = float(np.sqrt(np.mean((pred - reference) ** 2)))
    data_range = float(reference.max() - reference.min())
    psnr = float(psnr_sk(reference, pred, data_range=data_range))
    ssim = float(ssim_sk(reference, pred, data_range=data_range))

    # Flux recovery in masked region
    flux_ref  = float(np.sum(reference[mask]))
    flux_pred = float(np.sum(pred[mask]))
    flux_ratio = flux_pred / flux_ref if flux_ref > 0 else float("nan")

    return {
        "RMSE":          rmse,
        "PSNR_dB":       psnr,
        "SSIM":          ssim,
        "flux_ref":      flux_ref,
        "flux_pred":     flux_pred,
        "flux_recovery": flux_ratio,
    }
</code></pre>

<h2>7. Result Figure Template</h2>

<div class="result-placeholder"><span class="placeholder-label">RESULT SLOT · NOT DATA</span><div class="plot-grid"><div class="plot-box"><span>Reference image</span><b>Insert verified plot</b></div><div class="plot-box"><span>Dirty image</span><b>Insert verified plot</b></div><div class="plot-box"><span>CLEAN reconstruction</span><b>Insert verified plot</b></div><div class="plot-box"><span>U-Net reconstruction</span><b>Insert verified plot</b></div></div><p>Replace each slot with an exported experimental figure. Include dataset/case identifier, units (Jy/beam), shared colour scale (e.g., matplotlib 'inferno'), source mask contours at 3σ and 10σ, and a residual panel (pred − reference) wherever appropriate.</p></div>

<p class="small-note">No experimental values are included. Dashes in the metrics table are explicit placeholders, not measurements of zero or failure.</p>

</section>

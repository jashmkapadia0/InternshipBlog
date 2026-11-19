---
layout: default
title: Progress
description: Research progress timeline, experiment tracking, and planned milestones for the radio interferometric image reconstruction project at PRL.
permalink: /progress/
---
<section class="page-hero"><p class="eyebrow">03 / PROJECT LOG</p><h1>Progress, with evidence attached.</h1><p class="lede">A transparent timeline for decisions, implementation milestones, experiments, and review. Planned stages are labelled as planned; completed stages are documented with supporting evidence. No result is claimed until it can be traced to a reproducible experimental run.</p></section>

<section class="section content-grid"><div><p class="eyebrow">TIMELINE</p><h2>Research milestones</h2><p class="small-note">Statuses reflect the current project state and will be updated as work progresses.</p></div><div class="timeline">

<article class="timeline-item">
  <span class="timeline-date">STAGE 0</span>
  <div>
    <h3>Theoretical foundation and project scoping</h3>
    <p>Derive the Radio Interferometer Measurement Equation from the Van Cittert–Zernike theorem. Document UV sampling, dirty beam, Högbom CLEAN algorithm, and U-Net architecture as the basis for a reproducible comparison. Commit to evaluation metrics and fair-comparison principles before any experiment is run.</p>
    <span class="tag tag-current">DOCUMENTED</span>
  </div>
</article>

<article class="timeline-item">
  <span class="timeline-date">STAGE 1</span>
  <div>
    <h3>Dataset selection and forward model specification</h3>
    <p>Select or simulate a sky brightness dataset with documented provenance. Specify the array configuration (VLA-A / MeerKAT), frequency band, UV weighting scheme (Briggs robust=0), imaging grid (512×512, 0.5″/pixel), and noise model. Implement and validate the Python forward model: sky → visibilities → dirty image → PSF. Establish train / validation / test split (70/15/15) at the observation level to prevent leakage.</p>
    <span class="tag">PLANNED</span>
  </div>
</article>

<article class="timeline-item">
  <span class="timeline-date">STAGE 2</span>
  <div>
    <h3>Baseline CLEAN implementation and validation</h3>
    <p>Run Multi-Scale CLEAN (Cornwell 2008) using CASA tclean or WSClean on all training and validation dirty images. Record software version, loop gain (γ=0.1), threshold (3σ), scales ([0,5,15,30] arcsec), and restoring beam. Verify flux conservation: Σ I<sub>CLEAN</sub> / Σ I<sub>ref</sub> within 5% for point-source-dominated fields. Document wall-clock time per image on specified hardware.</p>
    <span class="tag">PLANNED</span>
  </div>
</article>

<article class="timeline-item">
  <span class="timeline-date">STAGE 3</span>
  <div>
    <h3>U-Net implementation, training, and validation</h3>
    <p>Implement RadioUNet in PyTorch (encoder: [64,128,256,512] channels; decoder: symmetric with skip connections). Train on (dirty, reference) pairs using combined MSE+SSIM loss (α=0.7, β=0.3), AdamW (lr=1×10⁻⁴), cosine LR schedule, 150 epochs, batch size 8. Apply geometry-preserving augmentation (flips, 90° rotations) only. Select checkpoint by minimum validation loss. Reproduce training from seed=42 on documented hardware.</p>
    <span class="tag">PLANNED</span>
  </div>
</article>

<article class="timeline-item">
  <span class="timeline-date">STAGE 4</span>
  <div>
    <h3>Held-out evaluation: CLEAN vs. U-Net</h3>
    <p>Apply both methods to the held-out test set (never seen during training or model selection). Compute RMSE, PSNR, SSIM, flux recovery ratio, and runtime for each test case. Generate visual comparison panels (reference / dirty / CLEAN / U-Net / residual) for ≥ 10 representative cases spanning point-source and extended-emission morphologies.</p>
    <span class="tag">PLANNED</span>
  </div>
</article>

<article class="timeline-item">
  <span class="timeline-date">STAGE 5</span>
  <div>
    <h3>Generalisation and ablation study</h3>
    <p>Test U-Net on UV coverages not seen during training (different array configuration or observation duration). Ablation: remove skip connections; replace MSE+SSIM loss with MSE alone; reduce training set size by 50%. Assess whether CLEAN degrades more or less gracefully than U-Net under distribution shift.</p>
    <span class="tag">PLANNED</span>
  </div>
</article>

<article class="timeline-item">
  <span class="timeline-date">STAGE 6</span>
  <div>
    <h3>Analysis, write-up, and final documentation</h3>
    <p>Interpret results in the context of the research questions. Document limitations: source morphology space covered by training data, noise levels tested, UV coverage range, and hardware constraints. Prepare publication-quality figures. Write final report only after all experimental results have been verified against the documented protocol.</p>
    <span class="tag">PLANNED</span>
  </div>
</article>

</div></section>

<section class="section metrics-section">
  <p class="eyebrow">EXPERIMENT TRACKER</p>
  <h2>Results — intentionally blank until measured</h2>
  <p>These values will be populated only when experiments are complete, results have been traced to a specific experimental run, and metric definitions match those fixed in the <a href="/InternshipBlog/methodology/">Methodology</a> page.</p>

  <div class="metric-cards">
    <div><span>RMSE (Jy/beam)</span><strong>—</strong><small>No measured value</small></div>
    <div><span>PSNR (dB)</span><strong>—</strong><small>No measured value</small></div>
    <div><span>SSIM</span><strong>—</strong><small>No measured value</small></div>
    <div><span>Flux recovery</span><strong>—</strong><small>No measured value</small></div>
    <div><span>CLEAN runtime (s/image)</span><strong>—</strong><small>No measured value</small></div>
    <div><span>U-Net inference (s/image)</span><strong>—</strong><small>No measured value</small></div>
  </div>

  <p class="small-note">Dashes explicitly indicate that no result has been obtained. They do not mean zero, failure, or omission. The number of metric cards will grow as experiments are added.</p>
</section>

---
layout: default
title: "From visibilities to images: framing the inverse problem"
date: 2026-11-19
categories: [foundations]
excerpt: "A project note on why radio interferometric imaging is a reconstruction problem, and which questions this project will document before comparing methods."
---
<p class="eyebrow">PROJECT NOTE · FOUNDATIONS</p>
<p class="lede">A radio image is reconstructed from measurements in the Fourier domain. That simple statement shapes the whole project.</p>
<h2>What the antennas measure</h2><p>A pair of antennas samples a complex visibility at a projected baseline in wavelengths. The set of measured baselines and their evolution with time form the UV coverage. Because that coverage is finite and incomplete, an inverse Fourier transform does not recover an unconvolved sky image directly.</p>
<h2>Why the dirty image is not the final answer</h2><p>The inverse transform of sampled visibilities produces a dirty image: the sky convolved with the point spread function, with sidelobes reflecting the sampling pattern. Deconvolution methods use assumptions or learned priors to estimate a cleaner image. Högbom CLEAN iteratively subtracts shifted PSF responses; a U-Net learns a mapping from examples. Both need validation against a defined reference.</p>
<h2>Questions to keep visible</h2><ul><li>What data or simulation will define the reference sky and its measurement process?</li><li>How will train, validation, and test cases be separated?</li><li>Will both methods use the same UV sampling, noise, image grid, and flux convention?</li><li>How will structural fidelity and integrated flux be considered alongside pixel error?</li></ul>
<p>These are open methodological questions, not completed steps. This journal will be updated when the project makes and records those decisions. There are no experimental results in this note.</p>

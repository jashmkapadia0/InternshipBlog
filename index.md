---
layout: default
title: Home
description: A research project on radio interferometric image reconstruction and deconvolution.
---

<section class="hero">
  <div class="hero-grid">
    <div class="hero-copy">
      <p class="eyebrow"><span class="status-dot"></span> RESEARCH PROJECT · PRL</p>
      <h1>Seeing the sky through <span>incomplete data.</span></h1>
      <p class="lede">Deep learning based reconstruction and deconvolution of radio interferometric images.</p>
      <p class="hero-detail">A project journal on how sparse Fourier measurements become scientifically useful images—and how a U-Net reconstruction can be evaluated alongside the established Högbom CLEAN method.</p>
      <div class="actions"><a class="button button-primary" href="{{ '/research/' | relative_url }}">Explore the research <span aria-hidden="true">→</span></a><a class="text-link" href="{{ '/progress/' | relative_url }}">View project progress</a></div>
      <p class="honesty-note"><span aria-hidden="true">●</span> No experimental results are reported here yet.</p>
    </div>
    <figure class="hero-figure">
      <img src="{{ '/assets/images/interferometry.svg' | relative_url }}" alt="Illustrative diagram showing antennas sampling spatial frequencies and forming an image of a radio source">
      <figcaption><span>FIG. 01</span> Conceptual view of an array sampling the Fourier plane. Illustration only; not observational data.</figcaption>
    </figure>
  </div>
  <div class="hero-meta"><span>01 <b>THE MEASUREMENTS</b></span><span>02 <b>THE RECONSTRUCTION</b></span><span>03 <b>THE EVALUATION</b></span></div>
</section>

<section class="section section-intro">
  <div class="section-heading"><p class="eyebrow">THE PROJECT</p><h2>From visibilities to a trustworthy image</h2></div>
  <div class="intro-text"><p>A radio interferometer does not directly record a photograph. Pairs of antennas measure complex visibilities: samples of the sky's spatial Fourier transform at locations set by their projected baselines.</p><p>Incomplete sampling leaves a dirty image blurred by the point spread function (PSF) and marked by sidelobes. This project studies reconstruction with Högbom CLEAN and a U-Net, with evaluation designed to preserve photometric and structural fidelity.</p><a class="arrow-link" href="{{ '/methodology/' | relative_url }}">Read the methodology <span>↗</span></a></div>
</section>

<section class="section dark-section">
  <div class="section-heading"><p class="eyebrow">RESEARCH MAP</p><h2>One workflow, two reconstruction paths</h2><p class="section-subtitle">A project-level plan, not a report of completed experiments.</p></div>
  <figure class="wide-figure"><img src="{{ '/assets/images/workflow.svg' | relative_url }}" alt="Research workflow from sky model and array sampling through visibilities and dirty image to CLEAN and U-Net reconstructions, then common evaluation"><figcaption><span>FIG. 02</span> Planned reconstruction and evaluation workflow. Specific datasets, parameters, and results will be documented when established.</figcaption></figure>
  <div class="feature-grid">
    <article><span class="feature-index">A / OBSERVE</span><h3>Understand the sampling</h3><p>Relate antenna baselines, UV coverage, complex visibility data, the dirty beam, and imaging artifacts.</p><a href="{{ '/research/' | relative_url }}">Research context →</a></article>
    <article><span class="feature-index">B / RECONSTRUCT</span><h3>Compare established and learned methods</h3><p>Document Högbom CLEAN and a U-Net approach, including data preparation, training, and inference choices.</p><a href="{{ '/methodology/' | relative_url }}">Methodology →</a></article>
    <article><span class="feature-index">C / VALIDATE</span><h3>Measure before concluding</h3><p>Use RMSE, PSNR, SSIM, flux recovery, and runtime with explicit references and reproducible conditions.</p><a href="{{ '/progress/' | relative_url }}">Progress and metrics →</a></article>
  </div>
</section>

<section class="section next-section"><div><p class="eyebrow">LATEST NOTE</p><h2>Starting with the measurement equation</h2><p>The first project note lays out the forward problem and the questions this work will test.</p></div><a class="button button-outline" href="{{ '/blog/' | relative_url }}">Read the project notes <span>→</span></a></section>

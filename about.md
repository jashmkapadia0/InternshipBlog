---
layout: default
title: About
description: About Jashm Kapadia's PRL internship research project on deep learning-based radio interferometric image reconstruction.
permalink: /about/
---
<section class="page-hero"><p class="eyebrow">ABOUT THIS JOURNAL</p><h1>A careful record of a research process.</h1><p class="lede">This site documents a research project conducted at the Physical Research Laboratory (PRL), Ahmedabad, on radio interferometric image reconstruction and deconvolution using deep learning.</p></section>

<section class="section content-grid"><div><p class="eyebrow">PROJECT SUMMARY</p><h2>Research question</h2></div><div class="prose">

<p><strong>Deep Learning-Based Reconstruction and Deconvolution of Radio Interferometric Images</strong> investigates how image reconstruction from incomplete interferometric measurements can be studied with both a classical deconvolution method and a learned model. The project is conducted at the <a href="https://www.prl.res.in/" target="_blank" rel="noopener">Physical Research Laboratory (PRL)</a>, Ahmedabad — one of India's premier space and atmospheric science research institutes, under the Department of Space, Government of India.</p>

<figure class="article-figure">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/9e/GMRT_Antenna_Array.jpg/1280px-GMRT_Antenna_Array.jpg" alt="The Giant Metrewave Radio Telescope (GMRT) near Pune, India — 30 fully steerable parabolic dishes of 45m diameter each, operated by NCRA-TIFR" onerror="this.src='/InternshipBlog/assets/images/interferometry.svg'" />
  <figcaption><span>FIG. 06</span> The Giant Metrewave Radio Telescope (GMRT), Pune, India. 30 × 45 m fully steerable parabolic dishes operating at 120 MHz – 1460 MHz. One of the world's largest and most sensitive radio telescopes at metre wavelengths. Credit: NCRA-TIFR / Wikimedia Commons (CC BY-SA 4.0).</figcaption>
</figure>

<p>The project centres on a rigorous, reproducible comparison between <strong>Högbom CLEAN</strong> (the dominant classical deconvolution algorithm in radio astronomy since 1974) and a <strong>U-Net convolutional neural network</strong> (a deep learning approach that learns a mapping from dirty to clean images from training data). That comparison must account for the measurement and image domains, data provenance, preprocessing, model training, and evaluation conditions. No claim about comparative performance is made until experiments have been run and independently checked.</p>

<h3>Why this matters</h3>
<p>The Square Kilometre Array (SKA) — currently under construction in South Africa and Australia — will produce data volumes exceeding 300 Petabytes per year. At this scale, classical CLEAN algorithms running on CPU clusters may become a bottleneck. Data-driven reconstruction methods like U-Nets, once trained, perform inference in a single neural network forward pass and can run on GPUs at greatly reduced latency. Understanding the trade-offs — image fidelity, flux accuracy, generalisation, and runtime — is a central question for the next generation of radio astronomy.</p>

<h3>What this site records</h3>
<ul>
  <li>Theoretical background on interferometric measurements, the measurement equation, UV sampling, the dirty image, and the PSF.</li>
  <li>The planned reconstruction workflow, implementation decisions for both CLEAN and the U-Net, and methodological commitments made before experiments begin.</li>
  <li>Progress notes — open questions, implementation challenges, and corrections — as the project develops.</li>
  <li>Experimental plots and quantitative results (RMSE, PSNR, SSIM, flux recovery, runtime) once experiments are complete and results are verified.</li>
</ul>

<p class="callout"><strong>Research integrity note.</strong> Diagrams on this site are explanatory illustrations, not observational data. Placeholder result panels are explicitly labelled and contain no fabricated measurements. Results will only be published here after they have been independently verified against the documented experimental protocol.</p>

<h3>Tools and software stack</h3>
<ul>
  <li><strong>Imaging / CLEAN:</strong> CASA (Common Astronomy Software Applications) or WSClean</li>
  <li><strong>Deep learning:</strong> PyTorch ≥ 2.0, pytorch-msssim, NumPy, SciPy</li>
  <li><strong>Astronomy data:</strong> Astropy, python-casacore</li>
  <li><strong>Visualisation:</strong> Matplotlib, DS9</li>
  <li><strong>Environment:</strong> Python 3.11, documented via requirements.txt and conda environment files</li>
  <li><strong>Site:</strong> Jekyll (GitHub Pages), written entirely in Markdown and HTML</li>
</ul>

<h3>Key references</h3>
<ul class="small-note">
  <li>Högbom, J.A. (1974). "Aperture synthesis with a non-regular distribution of interferometer baselines." <em>A&amp;AS</em>, 15, 417.</li>
  <li>Thompson, A.R., Moran, J.M., &amp; Swenson, G.W. (2017). <em>Interferometry and Synthesis in Radio Astronomy</em>, 3rd ed. Springer Open.</li>
  <li>Cornwell, T.J. (2008). "Multi-scale CLEAN deconvolution of radio synthesis images." <em>IEEE J-STARS</em>, 1(3), 204–211.</li>
  <li>Ronneberger, O., Fischer, P., &amp; Brox, T. (2015). "U-Net: Convolutional networks for biomedical image segmentation." <em>MICCAI</em>, LNCS 9351.</li>
  <li>Terris, M. et al. (2023). "Image reconstruction algorithms in radio interferometry: From handcrafted to learned regularization denoisers." <em>MNRAS</em>, 518(1), 604–622.</li>
  <li>Offringa, A.R. et al. (2014). "WSCLEAN: An implementation of a fast, generic wide-field imager for radio astronomy." <em>MNRAS</em>, 444(1), 606–619.</li>
</ul>

</div></section>

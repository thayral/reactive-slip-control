---
layout: default
title: Learning-based slip detection for adaptive grasp control
---

# Learning-based slip detection for adaptive grasp control in robotic manipulation

**PhD Thesis Project — Théo Ayral**  
CEA (Leti & List) · Université Paris-Saclay

<p style="margin-top:0.5rem;">
  <a href="#publications">Publications</a> ·
  <a href="#code--resources">Code & Resources</a> ·
  <a href="#setup--benches">Setup</a> ·
  <a href="#method">Method</a> ·
  <a href="#results">Results</a> ·
  <a href="#latency">Latency</a>
</p>

---

## Video

<!-- Vimeo embed (works on GitHub Pages) -->
<div style="max-width:900px;">
  <div style="position:relative; padding-bottom:56.25%; height:0; overflow:hidden;">
    <iframe
      src="https://player.vimeo.com/video/1056472782?h=0&title=0&byline=0&portrait=0"
      style="position:absolute; top:0; left:0; width:100%; height:100%;"
      frameborder="0"
      allow="autoplay; fullscreen; picture-in-picture"
      allowfullscreen>
    </iframe>
  </div>
</div>

---

## Context

Robotic manipulation in unstructured environments requires **stable grasps without excessive force**.
Humans solve this by sensing **incipient slip** and modulating grip forces rapidly.
This project investigates **learning-based slip detection** integrated into an **interpretable, model-based grasp stabilization loop**, enabling fast reactions and robust behavior in multi-fingered grasps.


## Problem: force coordination in multi-finger grasps {#problem}

In parallel-jaw grippers, preventing slip is often handled by a simple scalar increase of grip force.
In multi-finger grasps, the same strategy can inject an undesired net wrench and destabilize the object.
We target **slip-aware force coordination**: increase stability while preserving the object-level wrench.

<table>
  <tr>
    <td width="45%" valign="top" align="center">
      <img src="media/huynh_2020.png" width="360" alt="Simple parallel-jaw gripper">
      <br><br>
      <strong>Simple gripper</strong>
      <ul align="left">
        <li><strong>Parallel jaws</strong>, single DoF</li>
        <li><strong>Scalar</strong> grasp-effort command</li>
      </ul>
    </td>
    <td width="55%" valign="top" align="center">
      <img src="media/Trigrasp.png" width="460" alt="Multi-digit gripper grasp">
      <br><br>
      <strong>Multi-digit gripper</strong>
      <ul align="left">
        <li>Independent actuation of multiple-DoF fingers</li>
        <li>Requires <strong>coordination</strong> of contact forces</li>
      </ul>
    </td>
  </tr>
</table>

<em>Takeaway: slip recovery in multi-finger grasps is not a scalar “grip harder” action — it must coordinate contact forces.</em>

---

## Code & Resources

> Remplace les liens par tes repos / pages.

- **Demo (minimal runnable example):** https://github.com/thayral/<demo-repo>
- **Training / research code (implementation details):** https://github.com/thayral/<training-repo>
- **Slides (full, for deep dive):** https://thayral.github.io/phd-defense-slides/
- **Thesis manuscript (PDF):** https://github.com/thayral/<thesis-repo-or-pdf-link>

---

## Setup & benches

### TraceBot / manipulation platform
- Multi-finger gripper with **hybrid tactile sensing**
- PzE: high-bandwidth friction-vibration sensing (slip cues)
- PzR: spatial pressure/contact localization (contact geometry update)

<!-- TEMPLATE: image (static figure / photo) -->
<p>
  <img src="assets/figures/setup_photo.png" width="820" alt="Experimental setup photo">
</p>
<em>Figure: Multi-finger gripper instrumented with hybrid tactile pads.</em>

### Data collection benches
We rely on automated and parameterized benches to generate labeled slip events under controlled variability (object, speed, force, grasps) and to collect **non-slip perturbations** that mimic slip-like dynamics.

<!-- TEMPLATE: GIF for bench -->
<p>
  <img src="assets/gifs/bench.gif" width="820" alt="Automated slip bench">
</p>
<em>Automated bench for slip trajectory generation with ground-truth signals.</em>

---

## Method

### Overview
We use a **hybrid learning + model-based** approach:
- **Learned slip perception** from high-bandwidth tactile cues (FFT + GRU)
- **Online grasp model update** from contact localization
- **Event-triggered internal-force optimization** to stabilize the grasp without disturbing the object wrench

<!-- TEMPLATE: method schema -->
<p>
  <img src="media/rsc_overview.png" width="700" alt="Reactive slip control overview">
</p>
<em>Overview of the reactive slip control (RSC) pipeline.</em>



<table>
  <tr>
    <td valign="middle" align="center" width="38%">
      <img src="media/rsc_slip.png" width="280" alt="Image 1">
    </td>
    <td valign="middle" align="center" width="23%">
      <img src="media/rsc_feedback.png" width="280" alt="Image 2">
    </td>
    <td valign="middle" align="center" width="38%">
      <img src="media/rsc_geom.png" width="280" alt="Image 3">
    </td>
  </tr>
</table>




### Spectro-temporal features (PzE → FFT/PSD → Spectrogram)

<div style="width:100%; margin: 0 auto;">

  <video autoplay loop muted playsinline style="width:33%; height:auto; display:block; margin: 0 auto;">
    <source src="media/fft_pze.mp4" type="video/mp4">
  </video>

  <div style="height: 5px;"></div>

  <video autoplay loop muted playsinline style="width:20%; height:auto; display:block; margin: 0 auto;">
    <source src="media/fft_frames.mp4" type="video/mp4">
  </video>

  <div style="height: 5px;"></div>

  <video autoplay loop muted playsinline style="width:33%; height:auto; display:block; margin: 0 auto;">
    <source src="media/fft_spectro.mp4" type="video/mp4">
  </video>

</div>

<em>We process high-bandwidth PzE tactile signals in short windows, extract frequency-domain PSD features via FFT, and build a spectrogram for slip classification.</em>



<table style="width:100%;">
  <tr>
    <!-- LEFT: big image -->
    <td width="50%" valign="middle" align="center">
      <img src="media/FFT_GRU.png" style="width:100%; height:auto; display:block;" alt="Method overview">
    </td>

    <!-- RIGHT: smaller image + bullets below -->
    <td width="50%" valign="top" align="center">
      <img src="media/GRU_d2lai.png" style="width:70%; height:auto; display:block; margin: 0 auto;" alt="FFT-GRU slip detection pipeline">

      <div style="height:14px;"></div>

      <div style="text-align:left; display:inline-block; width:90%;">
        <ul>
          <li>Identify <strong>spectral patterns</strong> of friction</li>
          <li>Analyse <strong>temporal evolution</strong> with recurrence</li>
          <li><strong>100Hz classification</strong> with binary classes</li>
          <li><strong>Training</strong> with binary cross-entropy (BCE)</li>
        </ul>
      </div>
    </td>
  </tr>
</table>



## Generating perturbations for training {#perturbations}

<table>
  <tr>
    <td width="55%" valign="top">
      <strong>Perturbation taxonomy</strong>
      <ul>
        <li><strong>ΔF<sub>n</sub></strong> — Grasp effort variations: normal force (tighten / release)</li>
        <li><strong>ΔF<sub>t</sub></strong> — External load variations: tangential load (shear / traction)</li>
        <li><strong>Δq</strong> — Actuation noise: structural vibrations</li>
      </ul>
      <em>Goal: reduce false alarms while preserving sensitivity to real slip.</em>
    <td width="45%" align="center" valign="top">
      <img
        src="media/perturbations_taxonomy.png"
        width="420"
        alt="Perturbation taxonomy"
        style="transform: rotate(90deg);">
    </td>
  </tr>
</table>

<p>
  <img src="media/perturbations-rarity.png" width="900" alt="Perturbations imbalance">
</p>



    
###  Contact point estimation


    \includegraphics[width=\textwidth]{chap3/fig/coordinates image sensor (1).pdf}
    \includegraphics[width=0.6\textwidth]{Backups/pzr points sim.png}%
    \quad\includegraphics[width=0.3\textwidth]{Backups/pzr points visu.png}
---

## Results

### Experimental validation (example)
**Asymmetric 3-finger grasp on a cylinder (planar)**  
- Internal force coordination in **null space of the grasp**  
- RSC triggers after ~**130 ms**  
- ~**19 mm** object travel before stop

<!-- Put your key result figure / montage here -->
<p>
  <img src="media/Trigrasp_full_vertical.png" width="900" alt="Experimental validation figure">
</p>



---

## Latency

### Real-time loop & latency budget (theoretical compute)
This section summarizes the optimized in-loop compute budget (excluding I/O-heavy prototype constraints).

| Block | Estimate |
|------|----------|
| FFT (C impl., 20 ms window) | < 0.1 ms compute (windowing delay ~13 ms) |
| GRU inference (Python/ONNX) | ~3 ms (model decision delay ~24 ms) |
| PzR → contact + 𝒩(G) update | ~5 ms |
| QP solve (OSQP) for internal-force update | ~4 ms |
| **Net slip-reaction latency (target)** | **~35–40 ms** |

---

## Publications

- **AIM 2023 (published)**  
  *Spectro-Temporal Recurrent Neural Network for Robotic Slip Detection with Piezoelectric Tactile Sensor*  
  Théo Ayral, Saifeddine Aloui, Mathieu Grossard

- **(in preparation)**  
  *Robust Tactile Slip Detection under Manipulation Perturbations*  
  Théo Ayral, Saifeddine Aloui, Mathieu Grossard

- **ICRA 2026 (accepted)**  
  *Reactive Slip Control in Multifingered Grasping: Hybrid Tactile Sensing and Internal-Force Optimization*  
  Théo Ayral, Saifeddine Aloui, Mathieu Grossard

- **Patent application (2025)**  
  *Robotic gripper and control method*  
  M. Grossard, S. Aloui, T. Ayral — US Patent Application 19/011,931
---

## Contact

- Email: <your.email@cea.fr>
- Scholar / website: <link>
- GitHub: https://github.com/thayral

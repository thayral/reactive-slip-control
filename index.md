---
title: Reactive Slip Control in Multifingered Grasping
---


<script>
  window.MathJax = {
    tex: { inlineMath: [['$', '$'], ['\\(', '\\)']], displayMath: [['$$','$$'], ['\\[','\\]']] },
    options: { skipHtmlTags: ['script','noscript','style','textarea','pre','code'] }
  };
</script>
<script defer src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js"></script>




# Reactive Slip Control in Multifingered Grasping
Hybrid Tactile Sensing and Internal-Force Optimization

**Paper accepted for ICRA 2026**

[https://arxiv.org/abs/2602.16127](https://arxiv.org/abs/2602.16127)

Robotic manipulation in unstructured environments requires **stable grasps without excessive force**.
Humans solve this by sensing **incipient slip** and modulating grip forces rapidly.
This project investigates **learning-based slip detection** integrated into an **interpretable, model-based grasp stabilization loop**, enabling fast reactions and robust behavior in multi-fingered grasps.



keywords : grasp stability, internal forces, slip detection, multifingered gripper

<p style="margin-top:0.5rem;">
  <a href="#contact">contact</a>
</p>

---





## Context project
<div style="max-width:920px; margin:24px auto; padding:0 16px;">

  <div style="width:42%; margin:0 auto;">
    <a href="https://thayral.github.io/PhD-manipulation/" style="text-decoration:none; color:inherit;">
      <video autoplay loop muted playsinline style="width:100%; height:auto; display:block;">
        <source src="media/tracebot-process.mp4" type="video/mp4">
      </video>

      <div style="height:8px;"></div>

      <img src="media/grippermorphism.png" style="width:100%; height:auto; display:block;" alt="TraceBot use-case teaser">
    </a>

    <div style="margin-top:8px; display:flex; justify-content:space-between; align-items:flex-end; gap:10px;">
      <div style="font-size:0.95em; color:#444; line-height:1.2;">
        <strong>TraceBot use-case & platform context</strong><br>
        <a href="https://thayral.github.io/PhD-manipulation/" style="color:#444; text-decoration:underline;">
          Learn more on my PhD page <br> (setup, sensors, demos)
        </a>
      </div>

      <a href="https://thayral.github.io/PhD-manipulation/" title="PhD page: platform context">
        <img src="media/TraceBOT_logo.png" style="width:78px; height:auto;" alt="TraceBot logo">
      </a>
    </div>
  </div>

</div>




## CONTRIBUTION
*Closed-loop grasp adjustment*
Stabilize multi-fingered grasp without explicit friction models.

Closed-loop adaptation of grasp forces
Stabilize multi-finger grasps by injecting internal forces without explicit friction models
Tactile feedback loop leveraging slip detection for short reaction time

---


## Problem: force coordination in multi-finger grasps {#problem}

In parallel-jaw grippers, preventing slip is often handled by a simple scalar increase of grip force.
In multi-finger grasps, the same strategy can inject an undesired net wrench and destabilize the object.
We target **slip-aware force coordination**: increase stability while preserving the object-level wrench.


<div style="width:60%; max-width:980px; min-width:360px; margin:16px auto;">
  <table style="width:100%; border-collapse:collapse;">
    
    <tr>
      <td width="50%" align="center" valign="middle" style="padding:8px;">
        <img src="media/huynh_2020.png" style="width:70%; height:auto; display:block; margin:0 auto;" alt="Simple parallel-jaw gripper">
      </td>
      <td width="50%" align="center" valign="middle" style="padding:8px;">
        <img src="media/Trigrasp.png" style="width:70%; height:auto; display:block; margin:0 auto;" alt="Multi-digit gripper grasp">
      </td>
    </tr>

<tr>
  <td width="50%" valign="top" style="padding:8px;">
    <div style="text-align:center;">
      <strong>Simple gripper</strong>
    </div>
    <ul style="text-align:left; margin:8px 0 0 8px;">
      <li><strong>Parallel jaws</strong>, single DoF</li>
      <li><strong>Scalar</strong> grasp-effort command</li>
    </ul>
  </td>

  <td width="50%" valign="top" style="padding:8px;">
    <div style="text-align:center;">
      <strong>Multi-digit gripper</strong>
    </div>
    <ul style="text-align:left; margin:8px 0 0 8px;">
      <li>Independent actuation of multiple-DoF fingers</li>
      <li>Requires <strong>coordination</strong> of contact forces</li>
    </ul>
  </td>
</tr>

  </table>
</div>








### Why internal forces matter

<table style="width:100%; border-collapse:collapse; margin: 24px 0;">
  
  <!-- Row 1: headers -->
  <tr>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <strong>Uniform forces → Failure</strong>
    </td>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <strong>Internal force coordination → Stable grasp</strong>
    </td>
  </tr>

  <!-- Row 2: videos -->
  <tr>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <video autoplay loop muted playsinline style="width:95%; height:auto; display:block; margin:0 auto;">
        <source src="media/grasp_no_coordination.mp4" type="video/mp4">
      </video>
    </td>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <video autoplay loop muted playsinline style="width:95%; height:auto; display:block; margin:0 auto;">
        <source src="media/grasp_with_coordination.mp4" type="video/mp4">
      </video>
    </td>
  </tr>

  <!-- Row 3: equation / explanation -->
  <tr>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      $$
      \|\mathbf{f}_1\| = \|\mathbf{f}_2\| = \|\mathbf{f}_3\|
      $$
      <em>Uniform force magnitudes ignore grasp geometry and lead to slip.</em>
    </td>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <em>
        Internal forces injected in the null-space of the grasp matrix
        $\mathcal{N}(G)$ redistribute contact forces without disturbing object equilibrium.
      </em>
    </td>
  </tr>


</table>



---



## Tactile fingers - hybrid (PzE + PzR)


<div style="width:50%; margin:16px auto;">
  <img src="media/sensorshybrid.PNG" style="width:100%; height:auto; display:block;">
</div>





## Method Overview
We use a **hybrid learning + model-based** approach, with **two pipelines in parallel** and coupled through an **event-triggered feedback loop**:
- **Learned slip perception** from high-bandwidth tactile cues (FFT + GRU)
- **Online grasp model update** from contact localization
- **Event-triggered internal-force optimization** to stabilize the grasp without disturbing the object wrench

<table style="width:100%; border-collapse:collapse; margin:16px 0;">
  <tr>
    <td valign="middle" align="center" width="38%">
      <img src="media/rsc_slip.png" style="width:100%; height:auto; display:block;" alt="Slip perception pipeline (PzE)">
    </td>
    <td valign="middle" align="center" width="24%">
      <img src="media/rsc_feedback.png" style="width:100%; height:auto; display:block;" alt="Event-triggered feedback loop">
    </td>
    <td valign="middle" align="center" width="38%">
      <img src="media/rsc_geom.png" style="width:100%; height:auto; display:block;" alt="Grasp modeling + internal-force optimization (PzR)">
    </td>
  </tr>
</table>


<details>
  <summary><strong>Slip perception (PzE): training benches and signals</strong></summary>

  <div style="border:1px solid #ddd; border-radius:6px; padding:12px; margin:14px 0; background:#fafafa;">
    <table style="width:100%; border-collapse:collapse;">
      <tr>
        <td width="22%" valign="middle" align="center" style="padding:6px;">
          <img src="media/setuplab.png" style="width:100%; height:auto; display:block;" alt="Slip detection bench">
        </td>
        <td width="22%" valign="middle" align="center" style="padding:6px;">
          <img src="media/forFig6-start_31956.png" style="width:100%; height:auto; display:block;" alt="Example slip signal">
        </td>
        <td width="56%" valign="middle" style="padding:6px; color:#444;">
          <strong>Slip detection module</strong><br>
          The controller relies on a learning-based slip detector trained on dedicated benches.
          Data collection, perturbation modeling, and training are detailed on the project page.<br>
          <a href="https://github.com/thayral/tactile-slip-detection-pze">
            → Learn more about tactile slip detection
          </a>
        </td>
      </tr>
    </table>
  </div>

</details>





<details>
  <summary><strong>Grasp geometry (PzR): contact point estimation</strong></summary>

  <div style="max-width:920px; margin:14px auto; padding:0 16px; color:#444;">
    Contact points are estimated from the PzR pressure map and mapped from <em>sensor coordinates</em> to <em>finger/world coordinates</em>
    to update grasp geometry online.

    <div style="margin:16px auto;">
      <img src="media/coordinates_image_sensor.png" style="width:100%; height:auto; display:block;" alt="Coordinate frames and tactile sensor reference">
    </div>

    <table style="width:100%; border-collapse:collapse;">
      <tr>
        <td width="67%" valign="middle" align="center" style="padding:6px;">
          <img src="media/pzr_points_sim.png" style="width:100%; height:auto; display:block; margin:0 auto;" alt="Contact point estimation in simulation">
        </td>
        <td width="33%" valign="middle" align="center" style="padding:6px;">
          <img src="media/pzr_points_visu.png" style="width:100%; height:auto; display:block; margin:0 auto;" alt="Contact point visualization from PzR array">
        </td>
      </tr>
    </table>
  </div>

</details>

---



## Experimental validation


**Asymmetric 3-finger grasp on a cylinder (planar)**  
- Internal force coordination in **null space of the grasp**  
- RSC triggers after ~**130 ms**  
- ~**19 mm** object travel before stop

<p align="center">
  <img src="media/Trigrasp_full_vertical.png" width="600" alt="Experimental validation figure">
</p>



## Reactive Slip Control in action
**“peg-out” canister extraction**

<table style="width:100%; border-collapse:collapse; margin: 24px 0;">
  <tr>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <strong>Baseline → Failure (insufficient grasp force)</strong>
    </td>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <strong>RSC → Success (slip-triggered force increase)</strong>
    </td>
  </tr>

  <tr>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <video autoplay loop muted playsinline style="width:95%; height:auto; display:block; margin:0 auto;">
        <source src="media/canister_failure.mp4" type="video/mp4">
      </video>
    </td>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <video autoplay loop muted playsinline style="width:95%; height:auto; display:block; margin:0 auto;">
        <source src="media/canister_rsc_success.mp4" type="video/mp4">
      </video>
    </td>
  </tr>

  <tr>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <em>Object remains docked despite lifting motion.</em>
    </td>
    <td width="50%" align="center" valign="middle" style="padding:8px;">
      <em>Slip detected early → grasp effort increases → extraction succeeds.</em>
    </td>
  </tr>
</table>


---

## Latency

### Real-time loop & latency budget - theoretical compute
(optimized in-loop compute budget excluding I/O-heavy prototype constraints).

| Block | Estimate |
|------|----------|
| FFT (C impl., 20 ms window) | < 0.1 ms compute (windowing delay ~13 ms) |
| GRU inference (Python/ONNX) | ~3 ms (model decision delay ~24 ms) |
| PzR → contact + 𝒩(G) update | ~5 ms |
| QP solve (OSQP) for internal-force update | ~4 ms |
| **Net slip-reaction latency (target)** | **~35–40 ms** |


---
**Paper accepted at ICRA 2026**
*Reactive Slip Control in Multifingered Grasping: Hybrid Tactile Sensing and Internal-Force Optimization*

Théo Ayral, Saifeddine Aloui, Mathieu Grossard



## Contact
Théo AYRAL

➡️ This work is part of the PhD thesis   
**Learning-based slip detection for adaptive grasp control**  
CEA (Leti & List) · Université Paris-Saclay   
[https://github.com/thayral/PhD-manipulation](https://github.com/thayral/PhD-manipulation)

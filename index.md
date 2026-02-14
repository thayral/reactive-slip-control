---
layout: default
title: Learning-based slip detection for adaptive grasp control
---

# Learning-based slip detection for adaptive grasp control in robotic manipulation

**PhD Thesis Project — Théo Ayral**  
CEA (Leti & List) · Université Paris-Saclay



keywords : grasp stability, internal forces, slip detection, multifingered gripper

<p style="margin-top:0.5rem;">
  <a href="#publications">Publications</a> ·
  <a href="#code--resources">Code & Resources</a> ·
  <a href="#setup--benches">Setup</a> ·
  <a href="#method">Method</a> ·
  <a href="#results">Results</a> ·
  <a href="#latency">Latency</a>
</p>

---


**Paper accepted at ICRA 2026**
*Reactive Slip Control in Multifingered Grasping: Hybrid Tactile Sensing and Internal-Force Optimization*
Théo Ayral, Saifeddine Aloui, Mathieu Grossard


**Patent application:**
Robotic gripper and control method
M Grossard, S Aloui, T AYRAL
US Patent Application 19/011,931, 2025


## Context project
(TraceBot Project)
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
          Learn more on my PhD page (setup, sensors, demos)
        </a>
      </div>

      <a href="https://thayral.github.io/PhD-manipulation/" title="PhD page: platform context">
        <img src="media/TraceBOT_logo.png" style="width:78px; height:auto;" alt="TraceBot logo">
      </a>
    </div>
  </div>

</div>





## Context

Robotic manipulation in unstructured environments requires **stable grasps without excessive force**.
Humans solve this by sensing **incipient slip** and modulating grip forces rapidly.
This project investigates **learning-based slip detection** integrated into an **interpretable, model-based grasp stabilization loop**, enabling fast reactions and robust behavior in multi-fingered grasps.



## CONTRIBUTION
*Closed-loop grasp adjustment*
Stabilize multi-fingered grasp without explicit friction models.

Closed-loop adaptation of grasp forces
Stabilize multi-finger grasps by injecting internal forces without explicit friction models
Tactile feedback loop leveraging slip detection for short reaction time




## Problem: force coordination in multi-finger grasps {#problem}

In parallel-jaw grippers, preventing slip is often handled by a simple scalar increase of grip force.
In multi-finger grasps, the same strategy can inject an undesired net wrench and destabilize the object.
We target **slip-aware force coordination**: increase stability while preserving the object-level wrench.

<table>
  <tr>
    <td width="25%" valign="top" align="center">
      <img src="media/huynh_2020.png" width="360" alt="Simple parallel-jaw gripper">
      <br><br>
      <strong>Simple gripper</strong>
      <ul align="left">
        <li><strong>Parallel jaws</strong>, single DoF</li>
        <li><strong>Scalar</strong> grasp-effort command</li>
      </ul>
    </td>
    <td width="25%" valign="top" align="center">
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


+ PFANNE  ref ? 
  


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

##  sensors signals 






---


## Method Overview
We use a **hybrid learning + model-based** approach:
- **Learned slip perception** from high-bandwidth tactile cues (FFT + GRU)
- **Online grasp model update** from contact localization
- **Event-triggered internal-force optimization** to stabilize the grasp without disturbing the object wrench

<!--
<p>
  <img src="media/rsc_overview.png" width="700" alt="Reactive slip control overview">
</p>
-->



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


### Slip detection

<div style="border:1px solid #ddd; border-radius:6px; padding:12px; margin:24px 0; background:#fafafa;">
  <table style="width:100%; border-collapse:collapse;">
    <tr>
      <td width="30%" valign="middle" align="center">
        <img src="media/setuplab.png" style="width:100%; height:auto;" alt="Slip detection setup">
      </td>
      <td width="30%" valign="middle" align="center">
        <img src="media/forFig6-start_31956.png" style="width:100%; height:auto;" alt="Slip detection setup">
      </td>
      <td width="70%" valign="middle">
        <strong>Slip detection module</strong><br>
        The reactive slip controller relies on a learning-based tactile slip detector trained on dedicated benches.
        Data collection, perturbation modeling, and training are detailed separately.
        <br>
        <a href="https://github.com/thayral/tactile-slip-detection-pze">
          → Learn more about tactile slip detection
        </a>
      </td>
    </tr>
  </table>
</div>


    
### Contact point estimation

<div style="max-width:920px; margin:0 auto; padding:0 16px;">

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

    
---



### Experimental validation


**Asymmetric 3-finger grasp on a cylinder (planar)**  
- Internal force coordination in **null space of the grasp**  
- RSC triggers after ~**130 ms**  
- ~**19 mm** object travel before stop

<p>
  <img src="media/Trigrasp_full_vertical.png" width="900" alt="Experimental validation figure">
</p>




### Reactive Slip Control in action
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




## INTEGRATE CCL



## Code & Resources

## Contact
Théo AYRAL

# Aurora: A foundation model for the Earth system

Type: Paper
Link: https://www.nature.com/articles/s41586-025-09005-y

# Aurora: A foundation model for the Earth system

![image.png](Aurora%20A%20foundation%20model%20for%20the%20Earth%20system%201fb12b7265708083ab7dce6be5c41155/image.png)

## What is Aurora:

- **Parameters**: **1.3 B**
- **Goal**: One backbone that learns a unified 3-D latent state of the Earth and can be *lightly* fine-tuned for very different geophysical tasks.
- **Released task-specific checkpoints**

| Variant | Target quantity | Resolution | Lead time |
| --- | --- | --- | --- |
| **Aurora-AQ** | Global air-pollution chemistry | 0.4° | 5 days |
| **Aurora-Wave** | Deep-water significant wave height | 0.25° | 10 days |
| **Aurora-TC** | Tropical-cyclone tracks | point path | 5 days |
| **Aurora-WX-HR** | High-resolution weather | 0.1° | 10 days |

## Key Contributions

1. **Cross-domain pre-training**
Mixes reanalysis, deterministic and ensemble forecasts, and climate simulations in *one* loss.
2. **Unified 3-D backbone**
Perceiver-IO encoder → 3-D Swin Transformer processor → Perceiver-IO decoder; treats all variables as image tokens and collapses arbitrary pressure levels into a fixed latent depth.
3. **Memory-efficient fine-tuning**
Combines LoRA and the *push-forward trick* so multi-step roll-outs fit on a single A100 GPU.
4. **Orders-of-magnitude speed-up**
10-day global 0.1° forecast runs in ≈ 60 s on 1 × A100 versus ≈ 65 min on > 300 CPU nodes for ECMWF-IFS.

## Architecture:

Similar to GraphCast, **Aurora is organised into three stages**:

| Stage | Role | Implementation |
| --- | --- | --- |
| **Encoder** | Converts the input Earth-state fields to a latent 3-D representation. | **Perceiver-IO** encoder (patch embed + cross-attention). |
| **Processor** | Advances the latent state forward in time. | Stack of **3-D Swin Transformer** blocks. |
| **Decoder** | Maps the time-evolved latent state back to physical variables on the output grid. | **Perceiver-IO** decoder (cross-attention + patch reconstruction). |

![image.png](Aurora%20A%20foundation%20model%20for%20the%20Earth%20system%201fb12b7265708083ab7dce6be5c41155/image%201.png)

![image.png](Aurora%20A%20foundation%20model%20for%20the%20Earth%20system%201fb12b7265708083ab7dce6be5c41155/image%202.png)

All variables are first viewed as 2-D images of size **H × W**. Each image is split into **P × P** non-overlapping patches (Swim); every patch is flattened and linearly projected to a **D-dimensional** embedding.

A Perceiver-IO encoder then *reduces* the resulting patch tokens to a **fixed latent set of L tokens** through cross-attention.

The processor *evolves* these L tokens forward in time (forecast), and the decoder performs the inverse operation—expanding the L latent tokens back to the original patch grid and stitching the patches together—to reconstruct the Earth-state fields.

### Tensor shapes

| Stage | Atmosphere | Surface |
| --- | --- | --- |
| **Raw input** | **B × V<sub>A</sub> × C × T × H × W** | **B × V<sub>S</sub> × T × H × W** |
| **After patch-embed & latent pooling** | **B × L × D**<br>(pressure levels *C* aggregated to a fixed latent set) | **B × L × D** |

*B* batch size · *V<sub>A</sub>* # atmospheric variables · *V<sub>S</sub>* # surface variables · *C* native pressure levels · *T* context frames · *H*, *W* grid height/width.

Aggregating the original pressure levels into a **fixed latent depth** lets Aurora ingest datasets that provide *different* native level lists without changing the network shape.

## Training Data Mixture

| Category | Datasets / native resolution |
| --- | --- |
| **Reanalysis** | ERA-5 (0.25°), MERRA-2 (0.5°) |
| **Operational deterministic** | ECMWF-HRES (0.1° / 0.25°), NOAA-GFS (0.25°) |
| **Operational ensemble** | ECMWF-ENS (0.2°→0.1°), NOAA-GEFS reforecasts |
| **Climate simulations** | CMIP6 CMCC-CM2-VHR4, ECMWF-IFS-HR |
| **Specialised** | CAMS chemistry (0.4° / 0.75°), WAM wave (0.1° / 0.25°) |

*Validation set*: **2020** · T*est set*: **Jan 2022 – Aug 2023**

## Pre-training and Fine-tuning

### 1) Pre-training

| Item | Setting |
| --- | --- |
| **Objective** | Mean-absolute-error on a 6 h lead window |
| **Steps** | **150 000** optimizer steps |
| **Hardware** | **32 × NVIDIA A100-80 GB** GPUs (bf16) |
| **Data mix** | Forecasts + analyses + reanalyses + CMIP6 climate simulations (see §4) |

The model ingests the heterogeneous stream in a single training loop; no task heads are added at this stage.

---

### 2) Task-specific Fine-tuning

All weights remain trainable, but memory use is kept low by combining **LoRA** with the **push-forward trick** so that back-prop through 1–2 roll-out steps fits on a single A100.

| Target dataset (native grid) | Roll-out steps | Train steps |
| --- | --- | --- |
| **ECMWF-HRES T0 0.25°** | 2 | 8 000 |
| **ECMWF-HRES analysis 0.1°** | 1 | 12 500 |
| **CAMS reanalysis 0.4°** | 1 | 22 000 + 14 500 |
| **CAMS analysis 0.4°** | 1 | 7 500 + 5 500 |
| **HRES-WAM waves 0.25°** | 2 | 14 000 + 10 000 |

*Example — Air Quality.*

For **Aurora-AQ**, the model is fine-tuned on **CAMS** data from **May 2022 → Nov 2022** to predict

CO, NO, NO₂, SO₂, O₃, and particulate matter (PM₁, PM₂.₅, PM₁₀) at a 5-day lead.

---

**Take-away:** One heavy pre-train pass (150 k steps) + light LoRA fine-tunes (≤ 24 k steps) is enough to specialise the *same* 1.3 B-parameter backbone for weather, air-quality, wave, and cyclone tasks.

## Performance Highlights

| Task (lead / res.) | Metric | Aurora vs Baseline |
| --- | --- | --- |
| Weather 10 d / 0.1° | Global RMSE (850 hPa T) | Beats ECMWF-IFS on 92 % of vars |
| Weather 10 d / 0.25° | RMSE | Beats GraphCast on 91 % targets |
| TC track 5 d | Mean track error | 20–25 % lower than NOAA HWRF |
| Air quality 5 d / 0.4° | RMSE vs CAMS | Better on 74 % of pollutants |
| Wave height 10 d / 0.25° | RMSE (Hs) | 19 % lower than WAM analysis |

**Scaling law** Every 10× parameter increase ≈ 6 % relative error drop (37 M → 1.3 B).

### Conclusions

- **One backbone, many wins — but ensembles still missing**
    
    Aurora already beats specialised NWP systems on four very different tasks, yet it produces only a *single* deterministic forecast. Could be improved through ensemble forecasting.
    
- **Scaling curves still climbing**
    
    Neither parameter count (1.3 B) nor data diversity has hit a saturation point; extrapolation suggests larger models trained on an even broader mix could push error down another ≈ 6 % per 10× scale-up.
    
- **Still anchored to classical data-assimilation**
    
    Aurora could work from more data, like operating on observational data.
    

## References:

Z. Liu et al.,  *Swin transformer: Hierarchical vision transformer using shifted windows*, ICCV 2021

A. Jaegle et al.*, Perceiver IO: A General Architecture for Structured Inputs & Outputs*, ICLR 2022

E.J. Hu et al., *Lora: Low-rank adaptation of large language models ,* ICLR 2021

J. Brandstetter et al., *Message Passing Neural PDE Solvers*, ICLR 2022

A. Vaughan et al., Aardvark weather: end-to-end data-driven weather forecasting, Nature 2025

## Video:

https://www.youtube.com/watch?v=OqHlCXcibrg&t=646s

## Annexe:

### Datasets:

- **ERA5:** reanalysis dataset from ECMWF for weather (0.25°)
- **HRES forecasts:** High-resolution version of the operational NWP forecasting model run by ECMWF (0.1° and regridded 0.25°)
- **HRES T0:** Initial conditions to initialise the HRES, and forecasts quality ground truth.
- **HRES analysis:** Official analysis product of ECWMF, HRES-T0 + assimilation step.
- **IFS ENS:** Ensemble model (50 members) from ECMWF. 0.2° to 2023, and then 0.1°.
- **IFS ENS mean:** Mean predictions of IFS ENS.
- **GFS forecasts:** GFS 0.2° forecasts re-gridded to 0.25.
- **GFS T0:** like HRES T0 but for GFS.
- **GEFS reforecasts:** Ensemble version (5 members) of GFS, from 2009 to 2019.
- **CMIP6 (CMCC-CM2-VHR4 and ECMWF-IFS-HR):** Climate model of land, sea, atmosphere, and aerosol variables.
- **MERRA-2:** Atmospheric reanalysis dataset from NASA’S Global modelling and Assimilation Office, space-based observations of aerosols.
- **HRES-WAM:** Analysis and forecast data from ECMWF’s ocean wave (0.1° and re-grided 0.25° resolution).
- **CAMS:** Analysis and forecast data from the Copernicus Atmospheric Monitoring Service. 0.4° resolution of meteorological variables, atmosphere composition (like air pollutants).
- **CAMS reanalysis:** Reanalysis of CAMS at a resolution of 0.75°.
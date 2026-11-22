# Inverse-Designed Mach–Zehnder Interferometer (Meep)

Adjoint-based inverse design of a silicon-photonic Mach–Zehnder interferometer using [Meep](https://meep.readthedocs.io), runnable in Google Colab.

## What it does
1. **Coupler topology optimization:** a free-form 2×2 coupler in a 6 × 4 µm design region, optimized for a 50:50 split over 1.5–1.6 µm with low reflection.
2. **Path-difference inverse design:** ΔL is solved from a target peak wavelength and FSR, using the measured complex coupler amplitudes (t, k) and n_eff(λ).
3. **Verification:** FDTD of the full MZI compared with the circuit model and the target, plus GDS export.

## Method
- Objective: deviation of through/cross power from 0.5 plus a reflection penalty, averaged over the band
- Parametrization: mirror symmetry about y = 0, solid port stubs, conic filter (r = 0.15 µm), tanh projection with β = 2 → 64
- Optimizer: Adam on the adjoint gradient
- Final design binarized (β = 256) and re-simulated at higher resolution

## Parameters
| Parameter | Value |
|---|---|
| Technology | 220 nm Si on SiO2, TE-like, 2D effective index |
| Strip width | 450 nm |
| Port half-separation | 1.5 µm |
| Design region | 6 × 4 µm |
| Bend radius | 3 µm |
| Band | 1.5–1.6 µm |
| Target (editable) | `LAMBDA0 = 1.55 µm`, `FSR_TARGET = 0.05 µm` |

## Results (approximate, read from plots)
| Metric | Directional coupler | Inverse-designed coupler |
|---|---|---|
| Through / cross over 1.5–1.6 µm | -2.4 to -3.9 dB | about -3 to -3.5 dB |
| Reflection | not visible on plot | below -19 dB, about -35 dB near 1.535 µm |
| Footprint | more than 9 µm (S-bends alone) | 6 × 4 µm |

- **Inverse-designed ΔL:** 15.78 µm for a 1.55 µm peak and 50 nm FSR.
- **Full MZI (FDTD):** nulls near 1.525 and 1.575 µm (50 nm apart, as targeted); peak transmission about 0.8.
- **Known discrepancy:** FDTD fringes are about 8 nm to longer wavelengths than the circuit model and target, and peaks are lower than the model's about 0.88. The same kind of offset appears in the classical MZI, so a systematic phase contribution missing from the circuit model is suspected.

## Usage
1. Open `MZI_inverse_design.ipynb` in Colab and run the install cells.
2. Set `QUICK_TEST = True` first to check the pipeline, then `False` for the full run.
3. Optional: `GRAD_CHECK = True` compares the adjoint gradient with finite differences.

Outputs: `coupler_design_rho.npy`, `MZI_inverse_design_results.npz`, `MZI_inverse_design.gds`.

## Notes
The 2D effective-index model is an approximation. Confirm the final design with 3D FDTD before fabrication.

## Requirements
`pymeep`, `autograd`, `numpy`, `scipy`, `matplotlib`, `gdstk`.


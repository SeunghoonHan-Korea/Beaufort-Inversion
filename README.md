# Arctic FWI P-wave velocity models — Beaufort Sea, ARA05C line L05

Public data release accompanying the manuscript *(citation to be added)*.

Initial and final P-wave velocity (Vp) models from a 2-D multiscale full-waveform
inversion of a marine multichannel seismic profile acquired in the Beaufort Sea,
Canadian Arctic, during the Korean icebreaker **ARAON** expedition **ARA05C**
(line **L05**).

## Files

| File | Bytes | `nz` × `nx` | `dx` = `dz` | Extent (x × z) | Vp range |
|---|---|---|---|---|---|
| `vp_initial_10Hz.bin` | 2,508,800 | 160 × 3920 | 6.25 m | 24.49 × 0.99 km | 1.460 – 3.411 km/s |
| `vp_final_30Hz.bin` | 9,932,800 | 320 × 7760 | 3.125 m | 24.25 × 1.00 km | 1.450 – 3.735 km/s |

- `vp_initial_10Hz.bin` — initial model, built from diving-wave first-break
  traveltime picks, on the native 10 Hz inversion grid.
- `vp_final_30Hz.bin` — final conditioned model after 10/20/30 Hz FWI, on the
  30 Hz grid. **This is the published result.**

## Format

Flat **little-endian `float32`**, velocity in **km/s**, no header.
Stored as **`(nz, nx)` in C (row-major) order**: first axis depth, second axis
distance along the profile.

```python
import numpy as np
vp = np.fromfile("vp_final_30Hz.bin", dtype="<f4").reshape(320, 7760)   # km/s
```

- Depth is measured **from the sea surface**, distance from the western edge of
  the inverted window. Node *i* sits at exactly *i*·`dx`.
- The perfectly matched layer (40 cells each side and at the base) has been
  **stripped**; only the physical model region is distributed.
- The two models are on **different grids** — the 10 Hz grid is 250 m longer than
  the 30 Hz grid. Resample before differencing them.

## Acquisition

| | |
|---|---|
| Survey | ARA05C, line L05, Beaufort Sea (Canadian Arctic) |
| Vessel | IBRV *ARAON* (KOPRI) |
| Shots inverted | 448 (indices 33–480 of the 992-shot line) |
| Shot spacing | 50 m, source depth 9.4 m |
| Receivers | 120 channels per shot, 12.5 m spacing, depth 9.4 m |
| Streamer | trailing, offsets ≈ −94 m to −1581 m |
| Record length | 2.0 s |
| Water depth | ≈ 50 m |

The field data themselves are not redistributed here; they are held by the
Korea Polar Research Institute (KOPRI).

## Inversion

2-D acoustic, time-domain FWI with a joint Vp–Ip (impedance) parameterisation,
run under MPI. Only the Vp models are released.

| Band | Grid (`nx` × `nz`) | `dx` = `dz` | `nt` × `dt` | Iterations |
|---|---|---|---|---|
| 10 Hz | 3920 × 160 | 6.25 m | 4002 × 0.500 ms | 32 |
| 20 Hz | 7760 × 320 | 3.125 m | 6003 × 0.333 ms | 30 |
| 30 Hz | 7760 × 320 | 3.125 m | 6003 × 0.333 ms | 20 |

The 10 → 20 → 30 Hz bands are inverted in sequence, each starting from the
previous band's result. Updates use a fixed per-iteration step capping |ΔVp| at
75 m/s, with conjugate-gradient (Polak–Ribière) directions and a
Gaussian-smoothed, water-layer-masked gradient. The water layer (top ≈ 50 m) is
masked from the gradient and stays at its initial value.

## Post-inversion conditioning

The released final model is the raw 30 Hz FWI output **after a conditioning
step**, applied so the model is suitable for depth imaging and interpretation.
Two operations were applied, in order:

1. **Manual editing** of localised high-velocity artifacts immediately below the
   seabed.
2. **Anisotropic Gaussian smoothing**, σ<sub>z</sub> = 7 cells (≈ 22 m) and
   σ<sub>x</sub> = 1 cell (≈ 3 m), leaving the water layer untouched.

Relative to the raw FWI output the conditioning changes the model by
**0.097 km/s RMS overall**, concentrated at 50–250 m depth (0.18 km/s RMS at
50–125 m, falling to 0.04 km/s below 500 m). The largest single change is a
sub-seabed artifact reduced from 3.98 to 2.02 km/s. Deep structure (> 500 m) is
essentially unaltered. The unconditioned raw model can be made available on
request.

## Coordinates

Only the local profile coordinate (distance along the line from the western edge
of the inverted window) is provided. The navigation headers of the source SEG-Y
volume carry placeholder values, so no geographic georeferencing is distributed
here rather than publish an unverified one. Shot-point coordinates can be
supplied on request.

## Citation

> *(Manuscript citation to be added on acceptance.)*

Please also acknowledge the Korea Polar Research Institute (KOPRI) and the
ARA05C expedition as the source of the underlying seismic data.

## License

Released under [**CC BY 4.0**](https://creativecommons.org/licenses/by/4.0/).
See [LICENSE](LICENSE).

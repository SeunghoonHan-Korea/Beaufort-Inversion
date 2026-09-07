# Arctic FWI P-wave velocity models — Beaufort Sea, ARA05C line L05

Public data release accompanying the manuscript *(citation to be added)*.

Initial and final P-wave velocity (Vp) models from a 2-D multiscale full-waveform
inversion of a marine multichannel seismic profile acquired in the Beaufort Sea,


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


## Citation

> *(Manuscript citation to be added on acceptance.)*

Please also acknowledge the Korea Polar Research Institute (KOPRI) and the
ARA05C expedition as the source of the underlying seismic data.

## License

Released under [**CC BY 4.0**](https://creativecommons.org/licenses/by/4.0/).
See [LICENSE](LICENSE).

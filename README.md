<h1 align="center">impact-reg</h1>

<p align="center">
  Registers a moving volume to a fixed volume (NIfTI or OME-Zarr, e.g. microscopy) with impact-reg-konfai using FireANTs SyN, ConvexAdam or elastix presets, on GPU or CPU, and outputs the displacement field and moved image.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.975"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.975-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-see_LICENSE-lightgrey.svg" alt="License"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Registers a moving volume to a fixed volume (NIfTI or OME-Zarr, e.g. microscopy) with impact-reg-konfai using FireANTs SyN, ConvexAdam or elastix presets, on GPU or CPU, and outputs the displacement field and moved image.**

Wraps the impact-reg-konfai CLI (IMPACT multimodal registration): the engine selects a preset from the VBoussot/ImpactReg Hugging Face repository (FireANTs_SyN rigid+affine+SyN, ConvexAdam_Composite with MIND features, or elastix Generic_Rigid_BSpline), or preset_override names any other preset. Optional fixed and moving masks restrict the similarity metric. Use it for pairwise, possibly multimodal, registration of large volumes; give exactly one format (NIfTI or OME-Zarr) per volume. Presets are downloaded at run time, so the compute node needs internet access.

| | |
|---|---|
| **Inputs** | `neuro/microscopy/nifti` (optional) ×2 · `neuro/ome-zarr` (optional) ×4 · `neuro/mask` (optional) ×2 |
| **Outputs** | `neuro/transform/nifti` |
| **Container** | `gamorosino/app-impact-reg:0.1` |
| **Resources** | 4 GB memory · 5 h walltime |
| **Run it** | [doi.org/10.25663/brainlife.app.975](https://doi.org/10.25663/brainlife.app.975) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `fixed_nifti` | `neuro/microscopy/nifti` | no | Fixed/reference volume as NIfTI (alternative to fixed_ome_zarr). |
| `fixed_ome_zarr` | `neuro/ome-zarr` | no | Fixed/reference volume as an OME-Zarr store (alternative to fixed_nifti). |
| `moving_nifti` | `neuro/microscopy/nifti` | no | Moving volume as NIfTI (alternative to moving_ome_zarr). |
| `moving_ome_zarr` | `neuro/ome-zarr` | no | Moving volume as an OME-Zarr store (alternative to moving_nifti). |
| `fixed_mask_nifti` | `neuro/mask` | no | Optional fixed-space mask restricting the metric region, as NIfTI (alternative to fixed_mask_ome_zarr). Whole-image mask used if none given. |
| `fixed_mask_ome_zarr` | `neuro/ome-zarr` | no | Optional fixed-space mask as an OME-Zarr store (alternative to fixed_mask_nifti). |
| `moving_mask_nifti` | `neuro/mask` | no | Optional moving-space mask, as NIfTI (alternative to moving_mask_ome_zarr). Whole-image mask used if none given. |
| `moving_mask_ome_zarr` | `neuro/ome-zarr` | no | Optional moving-space mask as an OME-Zarr store (alternative to moving_mask_nifti). |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `warp` | `neuro/transform/nifti` | Displacement field (moving -> fixed) as a NIfTI vector image, plus the moved image and the raw ITK Transform.h5 as extra files. |

<details>
<summary><b>Parameters</b> (6)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `engine` | string | `fireants` | Registration engine/preset: fireants (FireANTs_SyN, GPU-capable diffeomorphic SyN), convexadam (ConvexAdam_Composite, MIND features), or elastix (Generic_Rigid_BSpline). |
| `preset_override` | string |  | Exact preset app name from the VBoussot/ImpactReg Hugging Face repo (e.g. FireANTs_Affine, ConvexAdam_Coarse, MR_CT_TS), overriding the engine mapping. Empty uses the engine's default preset. |
| `gpu` | boolean | `true` | Use a GPU if one is visible to torch. Disable to force CPU even when a GPU is present. |
| `require_cuda` | boolean | `false` | Fail instead of silently falling back to CPU when no GPU is visible. |
| `fields_only` | boolean | `false` | Write the displacement field only; skip computing the moved image. |
| `extra_set` | string |  | Comma-separated NAME=VALUE pairs, each forwarded as a separate --set to impact-reg-konfai (e.g. to tune iterations or patch size). Empty means none. |

</details>

<!-- brainlife-app:end -->

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.975](https://doi.org/10.25663/brainlife.app.975), choose `neuro/microscopy/nifti`, `neuro/ome-zarr`, `neuro/microscopy/nifti`, `neuro/ome-zarr`, `neuro/mask`, `neuro/ome-zarr`, `neuro/mask`, `neuro/ome-zarr` from your project, set the parameters and press **Execute**. The results are saved to your project as `neuro/transform/nifti`.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) (the App runs in `gamorosino/app-impact-reg:0.1`), [`jq`](https://jqlang.github.io/jq/).

1. Clone this repository.
2. Create `config.json` with the paths to your inputs and the parameters:

   ```json
   {
       "fixed_nifti": "/path/to/fixed_nifti/fixed_nifti",
       "fixed_ome_zarr": "/path/to/fixed_ome_zarr/fixed_ome_zarr",
       "moving_nifti": "/path/to/moving_nifti/moving_nifti",
       "moving_ome_zarr": "/path/to/moving_ome_zarr/moving_ome_zarr",
       "fixed_mask_nifti": "/path/to/fixed_mask_nifti/fixed_mask_nifti",
       "fixed_mask_ome_zarr": "/path/to/fixed_mask_ome_zarr/fixed_mask_ome_zarr",
       "moving_mask_nifti": "/path/to/moving_mask_nifti/moving_mask_nifti",
       "moving_mask_ome_zarr": "/path/to/moving_mask_ome_zarr/moving_mask_ome_zarr",
       "engine": "fireants",
       "preset_override": "",
       "gpu": true,
       "require_cuda": false,
       "fields_only": false,
       "extra_set": ""
   }
   ```

3. Run `./main`.

## Troubleshooting

- **no output produced**: Check the log for the impact-reg-konfai error; with require_cuda set, make sure a GPU is available.

## Engines

`engine` selects one of three generic presets from the default
`VBoussot/ImpactReg` Hugging Face repo (auto-downloaded on first use, not
baked into the container):

- `fireants` -> `FireANTs_SyN` -- Rigid+Affine+SyN diffeomorphic, GPU-capable.
  FireANTs is licensed under the FireANTs License v1.0 (see the preset's own
  `NOTICE`).
- `convexadam` -> `ConvexAdam_Composite` -- coarse+fine ConvexAdam chained in
  one app, IMPACT/MIND features.
- `elastix` -> `Generic_Rigid_BSpline` -- rigid + BSpline via elastix.

`preset_override` (advanced) takes any other exact preset name from that
repo (e.g. `FireANTs_Affine`, `ConvexAdam_Coarse`, `MR_CT_TS`), bypassing the
`engine` mapping.

## Why two venvs

elastix's binary is built against LibTorch 2.8/cu128; FireANTs and
ConvexAdam need torch 2.12/cu129. These can't share one Python environment
(same reason `impact-reg-ooc-demo`'s own `pixi.toml` keeps two pixi
environments). The container has `/opt/venv-default` (FireANTs, ConvexAdam)
and `/opt/venv-elastix` (elastix); `main.py` picks the right one based on
`engine`.

## Citing

If you use this App, please cite it, the software it builds on and brainlife.io:

- impact-reg. brainlife.io App. [doi:10.25663/brainlife.app.975](https://doi.org/10.25663/brainlife.app.975)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

- Gabriele Amorosino

## License

See [LICENSE](LICENSE).

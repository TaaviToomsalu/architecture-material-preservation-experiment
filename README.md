# Architecture Material Preservation Experiment

A small ComfyUI experiment exploring whether an image-to-image workflow can replace a stone facade with warm vertical wood siding while preserving the rest of an architectural image.

## Question

Can a Stable Diffusion 1.5 workflow make a localized facade material change without altering the house geometry or nearby paving and landscaping?

## Setup

- ComfyUI `v0.43.0`
- Checkpoint: `v1-5-pruned-emaonly-fp16.safetensors` (Stable Diffusion 1.5)
- Local hardware: AMD Radeon RX 6600, 8 GB VRAM
- Input: an architectural image generated with the same checkpoint
- Output resolution: 512 × 512

The checkpoint is not included. The inpainting workflow uses `assets/masked-input.png`, which contains the selected mask; copy it into ComfyUI's `input` folder before loading the workflow.

## Methods

### 1. Unmasked image-to-image

The source image was encoded with the checkpoint VAE and sampled with `denoise = 0.45`. The prompt requested replacing only the stone facade with warm natural vertical wood siding.

### 2. Masked inpainting

A mask was painted over the intended facade area and passed through `InpaintModelConditioning`. Two runs used the same prompt and mask, with `denoise = 0.45` and `denoise = 0.70`.

## Results

This is a qualitative visual comparison; no automated image similarity or geometry metric was used.

| Variant | Observation |
| --- | --- |
| Unmasked image-to-image, 0.45 | Changed parts of the surrounding scene, including foreground stone details. The edit was not localized. |
| Masked inpainting, 0.45 | Changes stayed within the selected area, but the result did not produce convincing vertical wood siding. |
| Masked inpainting, 0.70 | The selected area changed more, but the material still did not match the requested vertical wood siding. Increasing denoise did not solve the quality issue. |

## What I learned

- A text prompt alone did not reliably confine the edit to the facade.
- Masked inpainting improved spatial control and prevented edits outside the selected area.
- Spatial control did not guarantee material quality: the generated texture still missed the requested vertical-board appearance.
- Raising denoise from 0.45 to 0.70 did not produce a convincing material replacement in this setup.
- For a stronger result, the next experiment should compare an architecture- or inpainting-focused checkpoint, or a more controlled reference-based method, while keeping the mask fixed.

## Files

- `assets/source-generated.png` — initial generated image
- `assets/img2img-denoise-045.png` — unmasked image-to-image result
- `assets/inpaint-denoise-045.png` — masked result at 0.45
- `assets/inpaint-denoise-070.png` — masked result at 0.70
- `assets/masked-input.png` — image with the painted mask used for inpainting
- `workflows/img2img-workflow.json` — unmasked workflow
- `workflows/inpaint-workflow.json` — masked inpainting workflow

## Reproduction notes

Load the JSON workflow in ComfyUI and make sure the checkpoint is installed. For the inpainting workflow, place `assets/masked-input.png` in ComfyUI's `input` folder. The workflows were exported from generated PNG metadata and use the node layout and settings from those runs.

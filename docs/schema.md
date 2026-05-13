# `meta.json` schema

One `meta.json` per image, sitting next to the generated files in the same
folder.

```
div2KVF/0046/
├── hr.png
├── sr_x2.png
├── awb.png
├── depth.png
├── fg.png
├── seg.png
├── blur_h.png, blur_v.png, blur_d.png
├── noisy_l1.png ... noisy_l5.png
├── poisson_l1.png ... poisson_l5.png
└── meta.json
```

## Top-level fields

| Field           | Type    | Description                                                |
|-----------------|---------|------------------------------------------------------------|
| `id`            | string  | 4-digit image ID (e.g. `"0046"`)                           |
| `source`        | string  | Path to the original DIV2K HR image                        |
| `width`         | int     | Image width in pixels                                      |
| `height`        | int     | Image height in pixels                                     |
| `tasks`         | object  | All task-specific outputs (see below)                      |
| `files_present` | object  | Boolean flags per file (sanity check)                      |

## `tasks.sr` - Super-Resolution x2

```json
"sr": {
  "file": "sr_x2.png",
  "method": "mambair",
  "psnr_hat": 35.13,
  "psnr_mambair": 35.42,
  "ssim_hat": 0.948,
  "ssim_mambair": 0.951
}
```

* `method` is the per-image winner: `"hat"` or `"mambair"`.
* Both PSNR and SSIM are stored so users can re-select if needed.

## `tasks.awb` - Automatic White Balance

```json
"awb": {
  "file": "awb.png",
  "method": "fc4",
  "chroma_err_fc4": 0.088,
  "chroma_err_deepwb": 0.137,
  "fc4_illuminant_rgb": [0.546, 0.598, 0.587]
}
```

* `chroma_err_*` is computed as `L2(mean_BGR / mean_lum - 1)` — lower is better.
* `fc4_illuminant_rgb` is the global illuminant estimated by FC4 (RGB triplet).

## `tasks.depth` - Monocular Depth

```json
"depth": {
  "file": "depth.png",
  "method": "dav2",
  "edge_align_dav2": 0.387,
  "edge_align_midas": 0.394,
  "bit_depth": 16,
  "encoding": "inverse_disparity_uint16"
}
```

* Two models compared: **Depth-Anything V2 (Small)** and **MiDaS DPT_Large**.
* The selection metric is **Sobel edge-alignment Pearson correlation** (higher is better).
* Stored as 16-bit PNG, inverse disparity encoding.

## `tasks.caption` - Image Captioning

```json
"caption": {
  "method": "tag2text",
  "text": "a man running while holding a gun",
  "blip2": "a man in a military uniform",
  "tag2text": "a man running while holding a gun",
  "clip_score_blip2": 24.69,
  "clip_score_t2t": 28.17
}
```

* Selection metric: CLIP ViT-B/32 image-text cosine similarity.
* `text` mirrors whichever method was selected.

## `tasks.tags` - Visual tags

```json
"tags": ["gun", "soldier", "man", "run"]
```

Produced by Tag2Text. A compact list of salient visual concepts.

## `tasks.classification` - ImageNet top-1

```json
"classification": {
  "resnet50":     { "label": "military uniform", "score": 0.9933 },
  "efficientnet": { "label": "military uniform", "score": 0.6326 }
}
```

* Top-1 ImageNet class from each of two backbones.
* Confidence scores stored to allow consensus / uncertainty analysis.

## `tasks.foreground` & `tasks.segmentation`

```json
"foreground":   { "file": "fg.png"  }
"segmentation": { "file": "seg.png" }
```

* `fg.png` is the **union** of two saliency predictions (MobileNet + ResNet50).
* `seg.png` is the **ensemble** of DeepLabV3+ and FCN (ResNet-50 backbone).

## `tasks.blur` - Motion Blur (3 directions)

```json
"blur": {
  "horizontal": { "file": "blur_h.png" },
  "vertical":   { "file": "blur_v.png" },
  "diagonal":   { "file": "blur_d.png" }
}
```

## `tasks.poisson_noise` / `tasks.gaussian_noise`

```json
"poisson_noise": {
  "description": "Poisson noise at 5 SNR levels; near=bright convention",
  "levels": [
    { "level": 1, "peak": 255, "file": "poisson_l1.png" },
    { "level": 2, "peak": 100, "file": "poisson_l2.png" },
    { "level": 3, "peak":  50, "file": "poisson_l3.png" },
    { "level": 4, "peak":  20, "file": "poisson_l4.png" },
    { "level": 5, "peak":   5, "file": "poisson_l5.png" }
  ]
}
```

```json
"gaussian_noise": {
  "levels": [
    { "level": 1, "file": "noisy_l1.png" },
    { "level": 2, "file": "noisy_l2.png" },
    { "level": 3, "file": "noisy_l3.png" },
    { "level": 4, "file": "noisy_l4.png" },
    { "level": 5, "file": "noisy_l5.png" }
  ]
}
```

* Five SNR levels each.
* Poisson: `peak` controls signal-dependence.
* Gaussian: level index encodes increasing standard deviation.

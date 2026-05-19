# div2KVF v1.0

A multi-task image processing benchmark dataset built on 752 DIV2K HR images.

## Contents

```
div2KVF/
├── 0001/ ... 0752/         # one folder per image, 20 files each
│   ├── hr.png              # source HR
│   ├── sr_x2.png           # super-resolution x2
│   ├── awb.png             # white balance
│   ├── depth.png           # depth map (16-bit, inverse disparity)
│   ├── fg.png              # foreground / salient mask
│   ├── seg.png             # semantic segmentation
│   ├── blur_h/v/d.png      # motion blur (3 directions)
│   ├── noisy_l1..5.png     # Gaussian noise (5 levels)
│   ├── poisson_l1..5.png   # Poisson noise (5 levels)
│   └── meta.json           # all metadata, captions, tags, classification, scores
├── dataset_info.json       # global summary, schema and statistics
└── index.csv               # flat CSV of all image IDs
```

## Models used (winners marked in each `meta.json`)

| Task         | Models                                              | Selection                         |
|--------------|-----------------------------------------------------|-----------------------------------|
| SR x2        | HAT, MambaIRv2 Base                                 | PSNR + SSIM (per-image)           |
| AWB          | FC4, DeepWB                                         | Luminance-invariant chroma error  |
| Depth        | Depth-Anything V2 Small, MiDaS DPT_Large            | Sobel edge-alignment              |
| Caption      | BLIP-2 (OPT-2.7B), Tag2Text (Swin-14M)              | CLIP ViT-B/32 cosine similarity   |
| Segmentation | Ensemble of DeepLabV3+ and FCN (ResNet-50 backbone) | -                                 |
| Foreground   | Union of MobileNet and ResNet50 saliency            | -                                 |
| Tags         | Tag2Text                                            | -                                 |
| Classification | ResNet50, EfficientNet-B4                         | Both stored (top-1 + score)       |
| Blur         | 3 motion blur kernels                               | All kept                          |
| Noise        | Gaussian + Poisson                                  | 5 levels each                     |

## Quick start

```python
import json, cv2
from pathlib import Path

root = Path("div2KVF/0046")
meta = json.loads((root / "meta.json").read_text())

hr = cv2.imread(str(root / "hr.png"))
sr = cv2.imread(str(root / meta["tasks"]["sr"]["file"]))
print("Caption:", meta["tasks"]["caption"]["text"])
print("Tags:   ", meta["tasks"]["tags"])
```

## Citation

```bibtex
@misc{div2kvf_2026,
  author       = {Nasfi, Aya and Florea, Bianca-Maria and Hou, Jingkun},
  title        = {div2KVF: A Multi-Task Image Processing Benchmark Dataset},
  year         = {2026},
  howpublished = {EURECOM Semester Project}

@misc{div2kvf_2026,
  author       = {Jalmourzaev, Ibraguim},
  title        = {div2KVF: A Multi-Task Image Processing Benchmark Dataset for JPEG AI},
  year         = {2026},
  howpublished = {EURECOM Semester Project},
  note         = {Supervised by Abdellah El Mennaoui and Prof. Jean-Luc Dugelay}
}
```

## License

* Generated outputs (annotations, masks, depth, captions): CC BY 4.0
* Source HR images: see DIV2K terms — https://data.vision.ee.ethz.ch/cvl/DIV2K/

## Acknowledgments

* **Abdellah El Mennaoui** (PhD candidate, Eurecom) — daily supervision and technical guidance
* **Prof. Jean-Luc Dugelay** (Eurecom, Imaging Security Group) — project supervision
* Eurecom Imaging Security Group — for compute access (gravette GPU server)
* Authors of HAT, MambaIRv2, FC4, DeepWB, Depth Anything V2, MiDaS, DeepLabV3+, FCN, BLIP-2, Tag2Text, ResNet, MobileNet, EfficientNet — for releasing their models and code
* DIV2K dataset (Agustsson & Timofte, CVPRW 2017) — for the source HR images

## Full documentation

GitHub: https://github.com/ibraguim-jalmourzaev/MTDS_Div2K

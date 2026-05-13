# Anonymization step

Before any task is run, **all images containing visible faces are manually
removed** from `DIV2K_train_HR/`.

## Rationale

Although DIV2K is a publicly released dataset, we apply an extra anonymization
layer before generating downstream task ground truths, in line with EURECOM's
privacy guidelines:

* 800 original DIV2K HR images.
* Images containing recognizable faces are dropped.
* The released dataset contains **752 images**.

## Method

1. The 800 DIV2K HR images were inspected one by one.
2. Any image containing a recognizable face was removed.
3. The remaining 752 images form the working set for all subsequent tasks.

## Why not automated face detection?

Automated face detectors (e.g. MTCNN, RetinaFace) work well in most cases but
miss partial faces, side profiles, and faces at a distance. Given that the
dataset is small, manual inspection was deemed safer and faster than tuning
a detector threshold.

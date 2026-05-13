# Releasing the dataset on Google Drive

Step-by-step procedure for maintainers.

## 1. On the EURECOM server - package the dataset

```bash
ssh jalmourz@ssh.eurecom.fr
ssh gravette

cd /data/jalmourz
```

The dataset is already organized as one folder per image, each containing
all task outputs and a `meta.json`. Verify and zip:

```bash
# Verify a sample folder (should print 20 files)
ls div2KVF/0046/ | wc -l

# Count entries (should print 752)
ls -d div2KVF/[0-9]*/ | wc -l

# Verify dataset-level files exist
ls div2KVF/dataset_info.json div2KVF/index.csv

# Zip everything (may take 10-30 min, ~71 GB compressed)
zip -r div2KVF.zip div2KVF/
du -h div2KVF.zip
```

## 2. Download to your local machine

From your local terminal (not the server):

```bash
scp jalmourz@ssh.eurecom.fr:/data/jalmourz/div2KVF.zip ~/Downloads/
```

## 3. Upload to Google Drive

1. Open https://drive.google.com
2. **New > File upload** > select `div2KVF.zip`
3. After upload, right-click > **Share** > **General access** > **Anyone with the link**
4. **Copy link**

## 4. Update the README

Replace `https://drive.google.com/your-link-here` in `README.md` with the copied link:

```bash
git add README.md
git commit -m "docs: add Google Drive link to released dataset"
git push
```

## 5. Optional - create a GitHub release

Pin a documentation version that matches the dataset version on Drive.

1. Repo > **Releases** > **Draft a new release**
2. Tag: `v1.0`
3. Title: `div2KVF v1.0`
4. Description:
   ```
   First public release of div2KVF.

   Dataset (zipped): <Google Drive link>

   Contents:
   - 752 anonymized DIV2K HR images
   - Per-image outputs: sr_x2, awb, depth, fg, seg, blur x3, noise x10
   - Per-image meta.json (captions, tags, classification, scores)
   - dataset_info.json + index.csv at root
   ```
5. **Publish release**

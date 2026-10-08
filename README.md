# PhotoCraft crash samples

Large CLEM stitch documents that freeze [PhotoCraft](https://github.com/storytold/photocraft)'s canvas
when a layer's blend mode or opacity is changed — the visible image stops refreshing for a long time,
while zoom/pan keep working.

Context and source-level analysis:
[storytold/photocraft#1015](https://github.com/storytold/photocraft/issues/1015)

## Samples

Download them from the [Releases page](https://github.com/realjonnysax/photocraft-crash-samples/releases)
(GitHub's 100 MB git limit rules out committing them to the repo directly).

Stitched SEM canvases with fluorescence overlay layers, produced at the
[Center for Biologic Imaging, University of Pittsburgh](https://www.cbi.pitt.edu) from a
JEOL JSM-IT710HR tile-scan workflow (Ashlar stitching, 20% overlap).

| File | Size | Canvas (px) | Depth | Layers | Notes |
|---|---|---|---|---|---|
| `00004_ashlar.ome overlay.psb` | 685 MB | 7806 × 13729 | 8-bit RGB | 2 | overlay on blend **Color** @ 58% opacity; layer bounds extend past canvas |
| `00001_ashlar.ome copy.psb` | 1.4 GB | 15892 × 14626 | 8-bit RGB | 2 | overlay on blend **Color** @ 100% |
| `00003_ashlar.ome overlay.psb` | 1.6 GB | 15376 × 16127 | 8-bit RGB | 2 | overlay on blend **Color** @ 100%; layer bounds extend well past canvas (22450 × 20184) |

## Repro

1. Open a sample in PhotoCraft (v0.3.0, Windows; tested on 127.9 GB RAM, RTX 2070 8 GB, i7-6950X)
2. Change the overlay layer's blend mode or opacity
3. The canvas stops refreshing for a long time — the edit takes the full-refresh path, which falls
   back to the synchronous CPU compositor when the GPU working set doesn't fit the memory budget

Larger documents from the same workflow (up to 29119 px tall, a 6.4 GB PSB) freeze harder.

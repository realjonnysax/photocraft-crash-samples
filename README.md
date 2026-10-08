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
JEOL JSM-IT710HR tile-scan workflow: Ashlar stitching with the validated v7 registration
patch (padded FFT + multi-scale pair registration), 20% overlap, U-Net denoised tiles.

| File | Size | Canvas (px) | Depth | Layers | Notes |
|---|---|---|---|---|---|
| `00003_ashlar_v7.ome.psb` | 1.6 GB | 24892 × 12676 | 8-bit RGB | 2 | overlay on blend **Color** @ 100%; bounds extend past canvas (32441 × 16809) |
| `00004_ashlar_v7.ome.psb` | 1.6 GB | 18462 × 17869 | 8-bit RGB | 2 | overlay on blend **Color** @ 100%; bounds extend past canvas (20546 × 19675) |
| `00005_ashlar_v7.ome.psb` | 1.2 GB | 15480 × 14641 | 8-bit RGB | 2 | overlay on blend **Color** @ 100%; bounds extend past canvas (26152 × 27890) |
| `00006_ashlar_v7.ome.psb` | 1.9 GB | 21791 × 16186 | 8-bit RGB | 2 | overlay on blend **Color** @ 100%; bounds extend past canvas (33592 × 32546) |
| `00007_ashlar_v7.ome.psb` | 736 MB | 15260 × 10004 | 8-bit RGB | 2 | overlay on blend **Color** @ 100%, bounds match the canvas |
| `00008_ashlar_v7.ome.psb` | 1.5 GB | 20206 × 13292 | 8-bit RGB | 2 | overlay on blend **Color** @ 100%; bounds extend far past canvas (46840 × 33916) |

## Repro

1. Open a sample in PhotoCraft (v0.3.0, Windows; tested on 127.9 GB RAM, RTX 2070 8 GB, i7-6950X)
2. Change the overlay layer's blend mode or opacity
3. The canvas stops refreshing for a long time — the edit takes the full-refresh path, which falls
   back to the synchronous CPU compositor when the GPU working set doesn't fit the memory budget

The overlay layers extending past the canvas (up to 46840 × 33916 bounds on a 20206 × 13292 canvas)
enlarge the surfaces the compositor has to keep resident.

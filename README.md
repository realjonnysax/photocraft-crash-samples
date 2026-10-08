# PhotoCraft crash samples

Large CLEM stitch documents that expose two canvas problems in
[PhotoCraft](https://github.com/storytold/photocraft) on huge layers. Context and
source-level analysis: [storytold/photocraft#1015](https://github.com/storytold/photocraft/issues/1015)

**Layer blending and opacity only "freeze" when Free Transform is stuck active.** A lingering
transform session (its handles can sit off-screen, invisible on a huge canvas) blocks property
edits with *"Commit or cancel the transform first"* — so the sliders look dead. Press Enter to
commit (on these layers that itself is delayed — a multi-second CPU resample of the whole overlay),
and blend/opacity edits work again. Standard Photoshop adjusts blend/opacity mid-transform with
live preview; PhotoCraft blocks the edit until the transform is committed.

Separately, on our larger documents blend/opacity edits themselves take the full-refresh path and
fall back to a synchronous CPU composite when the GPU working set doesn't fit the memory budget —
the visible image stops refreshing for a long time (tracked in #1015).

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
2. Run Free Transform on the overlay layer — the handles can sit off-screen and stay invisible
3. Try to change blend/opacity — refused: the sliders look dead, "Commit or cancel the transform first"
4. Press Enter to commit — several seconds of silent blocking while the whole layer is resampled on the CPU
5. Blend/opacity edits then work on these 8-bit two-layer documents (GPU path holds); on our larger
   documents (up to 29119 px tall, a 6.4 GB PSB) the edit itself takes the CPU full-refresh path and
   the canvas stops refreshing for a long time

The overlay layers extending past the canvas (up to 46840 × 33916 bounds on a 20206 × 13292 canvas)
enlarge the surfaces the compositor has to keep resident.

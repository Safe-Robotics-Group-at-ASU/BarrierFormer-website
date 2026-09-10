Project website for **BarrierFormer: Transformer-Guided Predictive Barrier Enforcement for Safe Robot Control** (CoRL 2026).

Built on the same Nerfies/Bulma template as the VLM-GCBF+ website. To preview locally:

```bash
python -m http.server 8000   # then open http://localhost:8000
```

Total page payload is ~1.4 MB (21 videos + 2 figures).

## Video playback behaviour

Videos do **not** autoplay on page load. An `IntersectionObserver` at the bottom of
`index.html` starts each clip from its first frame when it scrolls into view (25%
threshold) and pauses it when it leaves; sources begin fetching 400px ahead of the
viewport. Without this, every clip ran on load and readers arrived at a grid of robots
already parked on their goals.

Any new `<video>` needs `data-autoplay-on-view` (plus `muted loop playsinline
preload="metadata"`) to join this behaviour — do not add an `autoplay` attribute.

The generalization clips are also **trimmed to roughly 1.2 s after the robot stops
moving**. The raw episodes run 1024 steps (34 s) but the robot typically reaches its
goal within the first 25–45%, leaving a long dead tail. Trim points were measured by
frame-differencing (`_source_media/` has the originals if you want to re-cut).

## Figures

Both were supplied as PDF and converted with `pdftoppm -png -r 200 -cropbox`. The
originals live in `../_source_media/`.

| File | Source |
| --- | --- |
| `figs/barrierformer_overview.png` | Fig. 1 — two-step training diagram (teaser) |
| `figs/generalization.png` | Fig. 2 — 3×3 generalization bar charts |

To update: replace the PDF in `../_source_media/`, re-run `pdftoppm`, overwrite the PNG.

## Videos

All were re-encoded for web (H.264, yuv420p, faststart, no audio). The 43 original
clips are archived in `../_source_media/video/` — nothing there is referenced by the
page, so the deploy stays small.

**Hero — predictive horizon (the best asset on the page):**

| File | Source |
| --- | --- |
| `vids/predictive_horizon_di.mp4` | `bf_DoubleIntegrator_area4_obs12_rank2_epi13_clr0.09.mp4`, 1400px wide, CRF 26 |

This is the only clip that visualizes the 6-step predicted plan, so it leads the
qualitative section. When you generate more in this style (including the **H = 18
ablation**), they belong here.

**Training distribution (l = 4, 8 obstacles), 700px, CRF 27:**

| File | Source |
| --- | --- |
| `vids/di_barrierformer.mp4` | `DI-SIM/0604-0935_DoubleIntegrator_n1_step4020_seed1234_epi48` |
| `vids/dc_barrierformer.mp4` | `DC/0604-0949_DubinsCar_n1_step3000_seed1234_epi52` |

**Generalization grid, 520px, CRF 31** — `gen_{di,dc}_l{4,6,8}_rho{050,075,100}.mp4`:

| Workspace | ρ = 0.50 | ρ = 0.75 | ρ = 1.00 |
| --- | --- | --- | --- |
| l = 4 | 8 obs | 12 obs | 16 obs |
| l = 6 | 18 obs | 27 obs | 36 obs |
| l = 8 | 32 obs | 48 obs | 64 obs |

Chosen from the `L4/`, `L6/`, `L8/` subfolders of each environment, preferring episodes
where the agent is mid-navigation near obstacles rather than already parked on the goal.

### Re-encoding a replacement

Keep the filename and the page needs no edit:

```bash
ffmpeg -i NEW.mp4 -t 9.1 -an -vf "scale=520:-2:flags=lanczos" \
  -c:v libx264 -crf 31 -preset slow -pix_fmt yuv420p -movflags +faststart \
  vids/gen_di_l4_rho050.mp4
```

`-t` is the trim length; pick it so the clip ends about a second after the robot
settles on the goal.

Use `-vf scale=700` / CRF 27 for the training-distribution row and `scale=1400` / CRF 26
for the hero. Browsers cache videos hard — hard-refresh (Ctrl+Shift+R) after replacing
one. If you swap a clip after the link is public, add `?v=2` to its `src` to bust caches.

## Not yet on the page

- **Crazyflie** — no CF videos were supplied, so the qualitative row shows DI and DC only.
  The CF *numbers* are in all the tables.
- **Baseline comparison** — no side-by-side footage. A clip of a baseline colliding where
  BarrierFormer does not would be the single most persuasive addition.
- **H = 18 predictive-horizon ablation** — planned.

## Releasing the Paper / arXiv / Code buttons

All three header buttons currently read **"… (Coming Soon)"** and are non-clickable
(greyed via the `button-coming-soon` CSS class). To turn one on, search `index.html`
for `TO RELEASE` — each button has a comment saying exactly what to change. In short:

1. add the `href` (for Paper it is already written in the comment: `./files/BarrierFormer.pdf`),
2. remove `button-coming-soon` from the `class` list,
3. change the label back to `Paper` / `arXiv` / `Code`.

If an artifact is never going to be released, delete its whole `<span class="link-block">` block.

Separately, the author list has Anandsingh Chauhan as plain text; wrap it in an `<a href="...">`
once a personal page exists (marked with a `TODO` comment).

## Website License

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

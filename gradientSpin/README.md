# Gradient Spin

A full-screen fork of [BIAsia/gradient-spin](https://github.com/BIAsia/gradient-spin) (MIT).

The original is a loading spinner: a small grid of `<span>` cells sharing one CSS keyframe that animates opacity, where each cell's `animation-delay` comes from a distance function over the grid.
Cells at equal distance light together, so the distance function *is* the wavefront shape.
Colors are sampled from multi-stop gradients in OKLab, because a straight sRGB interpolation detours through gray between hue families.

This fork keeps those ideas and drops the constraints that came from being DOM and CSS.

## What changed

- **Cells are fragments, not nodes.** The whole grid is one shader on a full-screen quad, so it runs at thousands of cells instead of nine.
- **The wavefront is continuous.** The original keyframe is a single fixed comet: full to dim over 45% of the cycle, hold, snap back over the last 8%. Here the leading edge, the trail and the falloff curve are all parameters. Attack 0.08, decay 0.45 and a linear curve reproduce the original exactly.
- **`bands` generalizes the tiling.** The original hardcodes a four-cell scroll period to stop the loop teleporting on wrap. Here it is one continuous control that applies to every pattern, not just the two scrolling ones.
- **Nine patterns instead of four.** The four originals are ported faithfully. Radial, spiral, radar sweep, two-source interference and turbulence only make sense at this scale.
- **The wave drives more than opacity.** Cells also swell at the crest, and can drag the color ramp along with them.
- **Everything composites in linear light.** The OKLab ramp is evaluated per fragment, bloom rolls off through a hue-preserving shoulder rather than clipping to white, and the sRGB encode happens once at the end.
- **A harmony accent rides the crest.** The accent is derived from the palette by color theory - complementary, split, analogous or triadic - then softened toward pastel, so the leading edge reads as a chromatic event and not just as more light.

All eight original palettes are carried over verbatim: `sunrise`, `bubble`, `peach`, `tonic`, `mint`, `spring`, `twilight`, `bay`.

## Use

Open `index.html`.
Everything is in the one file, with Three.js and lil-gui from a CDN.

`?preset=Aurora%20Spiral` loads a preset on startup.
The presets are Homage, Ripple Wall, Aurora Spiral, Silk Interference, Snake Raster, Dawn Drift and Diamond Sweep.

Under `prefers-reduced-motion` the piece starts paused on a static mid-sweep frame, which is what the original does.

Export and Import copies settings as JSON, saves a PNG, or records six seconds of MP4.
Settings copy falls back to a `prompt()` dialog where the Clipboard API is blocked.

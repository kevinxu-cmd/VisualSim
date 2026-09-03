# Gradient Spin

Two forks of [BIAsia/gradient-spin](https://github.com/BIAsia/gradient-spin) (MIT), pulling in opposite directions.

The original is a loading spinner.
A small grid of `<span>` cells shares one CSS keyframe that animates opacity, and each cell's `animation-delay` comes from a distance function over the grid.
Cells at equal distance light together, so the distance function *is* the wavefront shape.
Colors are sampled from multi-stop gradients in OKLab, because a straight sRGB interpolation detours through gray between hue families.

- **`index.html`** blows the idea up into a full-screen field.
- **`index-spinner.html`** stays at the size the original actually runs at and reproduces its behavior exactly.

Both carry the same twenty palettes.

## index.html - the field

The whole grid is one fragment shader on a full-screen quad, which removed the ceiling that shaped every other decision upstream.

- **Cells are fragments, not nodes**, so it runs at thousands of cells instead of nine.
- **The wavefront is continuous.** The upstream keyframe is one fixed comet: full to dim over 45% of the cycle, hold, snap back over the last 8%. Leading edge, trail and falloff curve are separate controls here. Attack 0.08, decay 0.45 and a linear curve reproduce the original exactly.
- **`bands` generalizes the tiling.** Upstream pins a four-cell scroll period so the loop does not teleport on wrap. That is now one control applying to every pattern.
- **Nine patterns instead of four.** The four originals are ported faithfully. Radial, spiral, radar sweep, two-source interference and turbulence only make sense at this scale.
- **The wave drives more than opacity.** Cells also swell at the crest and can drag the color ramp with them.
- **Everything composites in linear light.** The OKLab ramp is evaluated per fragment, and bloom rolls off through a hue-preserving shoulder rather than clipping to white.
- **A harmony accent rides the crest**, derived from the palette by color theory and softened toward pastel, so the leading edge reads as a chromatic event and not just as more light.

Presets: Homage, Ripple Wall, Aurora Spiral, Silk Interference, Snake Raster, Dawn Drift, Diamond Sweep.
`?preset=Aurora%20Spiral` loads one on startup.

## index-spinner.html - the spinner

A square grid, 2x2 to 5x5, at the scale the source was built for.

Faithful to the source:

- The four original patterns, ported line for line, including the four-cell scroll tiling.
- The per-cell table is built the way the React component builds it: a distance function per cell, phase = d / (max + 1), and for the `path` color axis a spatial-tiebreak ranking so every cell lands on its own point of the ramp instead of collapsing to a handful of samples.
- The original CSS keyframe exactly, with both breakpoints exposed rather than hardcoded.
- Cells composite over the background in sRGB, which is the space a browser blends CSS opacity in. What you see is what the DOM version would show.

Two defaults depart from the source, both a slider away from parity.
Resting opacity is 0.28 rather than 0.10, because 0.10 erases the resting matrix on a full screen and the palette disappears with it.
The color axis defaults to `path` rather than `row`, because on an n x n grid `row` shows only n colors while `path` shows the whole ramp.

What it adds:

- **A contact sheet** that draws every palette at once so the set can be read side by side. Click any tile to open it on its own.
- **An additive halo** summed over every cell rather than attributed to the nearest one. Nearest-cell attribution puts a hard seam down every line equidistant from two cells, because neighbors sit at different opacities.
- **Resolution independence.** The grid is drawn from signed distance fields, so it stays crisp from a favicon to a wall.

`?palette=dusk&pattern=snake&size=4&layout=sheet` sets up a view on startup.

## Palettes

The eight from the source, carried over verbatim: `sunrise`, `bubble`, `peach`, `tonic`, `mint`, `spring`, `twilight`, `bay`.

Twelve more in the same house style, a light desaturated head falling into a saturated tail, each built on a named color relationship:

| | | |
|---|---|---|
| `ember` analogous warm | `lagoon` analogous cool | `orchid` analogous violet |
| `moss` analogous green | `cobalt` monochromatic blue | `apricot` complementary span |
| `plum` monochromatic magenta | `slate` near-neutral | `citrus` analogous warm |
| `rose` monochromatic red | `arctic` monochromatic cool | `dusk` split-complementary |

## Use

Open either file.
Everything is in the one file, with Three.js and lil-gui from a CDN.

Under `prefers-reduced-motion` both start paused on a static mid-sweep frame, which is what the original does.

Export and Import copies settings as JSON, saves a PNG, or records six seconds of MP4.
Settings copy falls back to a `prompt()` dialog where the Clipboard API is blocked.

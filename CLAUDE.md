# Jang Seeder Wheel — Repo Notes

Parametric Jang-style seed roller, OpenSCAD. Fork of
[Fordi/jang-seeder-wheel](https://github.com/Fordi/jang-seeder-wheel).

## Files

- `seed_roller.scad` — the model. Top-level vars become Customizer
  inputs and are mirrored as keys in `seed_roller.json`.
- `seed_roller.json` — OpenSCAD preset file (`fileFormatVersion: 1`),
  one entry per known Jang roller plus any custom variants.
- `render.sh` / `render.bat` — wrap headless OpenSCAD. Auto-shells into
  Docker if `docker` is on PATH and we're not already inside `/input`.
- `Dockerfile` — `openscad/openscad:dev` + `jq` + `procps`.

STL outputs land in `./stl/` and are gitignored.

## Rendering

```sh
./render.sh list                # list presets
./render.sh "Jang N-6"          # render one
./render.sh all                 # render all (parallel, ~minute each)
```

Direct (skips the Docker shell-in):

```sh
openscad seed_roller.scad -p seed_roller.json -P "Jang N-6" \
  --render --export-format binstl -o "stl/Jang N-6.stl"
```

## Seed-cell geometry cheat sheet

When tweaking a preset, the slot/sphere/etc. dimensions are not all
direct knobs — some come from the wheel geometry.

For `seed_shape = "slot"`:

| Dimension                       | Formula                              |
| ------------------------------- | ------------------------------------ |
| Length along circumference      | `seed_size`                          |
| **Width across wheel face**     | `wheel_width - 2 * rim_thickness`    |
| Depth into wheel                | `seed_depth`                         |

So to widen the slot across the wheel (the short axis a bean wedges
into) without changing the wheel's overall width, **drop
`rim_thickness`**, don't touch `wheel_width` (the wheel needs to stay
20mm to fit the seeder housing).

## Gotcha: `min_rim_slope` depends on `rim_thickness`

`seed_roller.scad` derives:

```
min_rim_slope = max(rim_slope, 1.5 * (seed_depth + seed_countersink_depth - rim_thickness))
```

So lowering `rim_thickness` makes the inner rim slope steeper. Usually
fine, but worth knowing it's not a purely-local change.

## Variants

Keep original presets intact. Add tweaked versions as new entries with
a descriptive suffix, e.g. `Jang N-6 Wide`, and set `text` to something
that fits the label area and visibly distinguishes the print (e.g.
`N-6+`). Inserting in alphabetical-ish order next to the base preset
makes diffs easier to read.

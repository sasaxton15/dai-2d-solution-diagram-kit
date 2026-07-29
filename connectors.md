# Connectors

## Geometry

Paths are cubic beziers between container border midpoints — never between inner
elements, which drift when padding changes.

```
right-wall midpoint  = (left + width, top + height/2)
left-wall midpoint   = (left,         top + height/2)
```

Horizontal S-curve template:

```
M {Ax} {Ay} C {Cx} {Ay} {Cx} {By} {Bx} {By}     where  Cx = Ax + (Bx - Ax)/2
```

Coordinates come from the CSS `style` attributes, not from rendered pixels, so they
stay correct regardless of the stage scale.

## Fan-out

Every route from the hub departs a **single junction point**, marked with a small
filled circle in the hub hue. A shared origin gives the eye one place to start; per-node
origins read as unrelated lines.

When three or more destinations fan out, shift the destination bounding box so its
midpoint aligns with the source, then redistribute the interior destinations evenly.
Without that second step the middle routes visibly bow.

## States

Paths carry `data-c`: `s` = source, `h` = hub, `p` = partner/sidecar.

| Path | Idle | Active (`.hot`) |
|---|---|---|
| Source | 2px, opacity .6 | 3px, marching dash `7 9`, ~1.15s |
| Hub | 2px, opacity .72 | 3px, marching dash `7 9`, ~1.05s |
| Sidecar | 1.75px, opacity .55, dash `5 5` | ~1.35s dash animation |

Transitions on `opacity` and `stroke-width` run at `.4s`. Slightly different dash
periods per lane keep the lanes from visually locking into one rhythm.

Two motion idioms are both valid — marching dashes, or opacity/width pulsing. Pick
one per diagram and stay consistent within it.

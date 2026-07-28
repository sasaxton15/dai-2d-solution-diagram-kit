# Design system

## Canvas

```
viewBox = "0 0 1240 700"
```

Fixed pixel canvas, absolutely positioned children, 1:1 coordinate mapping between
CSS `left`/`top` values and SVG coordinates. `preserveAspectRatio="none"` on the SVG
so the overlay tracks the stage exactly.

Scaling is done by transforming the whole stage, never by re-laying-out:

```js
const s = container.clientWidth / 1240;
stage.style.transform = 'scale(' + s + ')';
container.style.height = (700 * s) + 'px';
```

Driven by `ResizeObserver`. Nothing re-wraps, so a label that fits at one size fits
at every size.

## Tokens

```css
--canvas:#0D1119;   /* stage background        */
--ink:#FFFFFF;      /* node titles             */
--muted:#8B93A7;    /* sub-labels, zone titles */
--border:#2C3548;   /* node and zone hairlines */
--node:#151C29;     /* default node fill       */

--source: <hue>;    /* left lane   */
--hub:    <hue>;    /* centre lane */
--sidecar:<hue>;    /* below       */
```

Three semantic hues. Tints are derived from the hub hue at `.10` alpha for fills and
`.65` for borders.

## Type scale

| Element | Size | Weight | Treatment |
|---|---|---|---|
| Node title | 11.5px | 600 | `--ink` |
| Node sub-label | 7px | 600 | uppercase, `.1em` tracking, `--muted` |
| Primary node title | 12.5px | 600 | white on hub hue |
| Zone title | 8.5px | 700 | uppercase, `.14em` tracking, `--muted` |
| Destination chip | 10px | 600 | dark ink on off-white, `nowrap` |

Small type is intentional. The diagram is a picture first; the labels identify
elements rather than carrying the argument. If a label needs more than a few words,
it belongs in a tooltip or the surrounding copy.

## Zones

```css
.zone        { border-radius:12px; border:1px solid var(--border); background:#131925; }
.zone.z-hub  { background:<hub 10%>; border-color:<hub 65%>; border-left:3px solid var(--hub); }
.zone.z-side { background:<sidecar 5%>; border:1px dashed <sidecar line>; border-radius:10px; }
```

The solid left rail on the hub zone is the single strongest signal that this lane is
different from the others. Keep it.

## Nodes

```css
.node         { background:var(--node); border:1px solid var(--border); border-radius:8px; }
.node.lit     { border-color:var(--hub); background:<hub darkened>; }
.node.primary { background:var(--hub); border-color:var(--hub); }
```

Exactly one `.primary`. `.lit` is a transient animation state, not a style you author
into the markup.

## Destination chips

```css
.tile { height:32px; background:#ECEEF1; border:1px solid #D3D7DE; border-radius:8px; }
```

Off-white `#ECEEF1`, not `#FFF`. This was changed after pure-white chips were found
to out-shout the hub and pull the eye to the right edge of the canvas.

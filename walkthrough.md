# Walkthrough: building a diagram from the template

Worked example — a diagram showing enterprise workloads reaching AI providers
through a connectivity layer.

## 1. Name the three lanes

Before touching code, answer these. If you can't, the diagram isn't ready.

- **Source** — enterprise apps, agents, private data
- **Hub** — the connectivity layer (this is the thing the diagram is *about*)
- **Destinations** — model APIs, GPU clouds, hyperscalers
- **Sidecar** — security and policy partners

The hub test: if you can't name a single element as the hub, you have a topology
picture rather than a solution diagram, and it will not land.

## 2. Fork and rename

```
cp template/diagram-template.html enterprise-ai-connectivity.html
```

Rename `#dgm` → `#eac` everywhere. In an editor, replace `dgm` with `eac` across the
file, then confirm the JS `getElementById` calls and the `keyframes` name changed too.

## 3. Set the palette

```css
#eac{
  --source:  #086AE3;
  --hub:     #E91C24;
  --sidecar: #7739D9;
}
```

Three hues. Update the tint values derived from `--hub`.

## 4. Fill the lanes

Source nodes get a title and a short uppercase sub-label:

```html
<div class="dgm-node" style="left:66px;top:212px;width:236px;">
  <b>AI agents</b><span>Autonomous · multi-step</span></div>
```

Hub lane: the connective product as `.primary`, supporting capabilities beneath it as
ordinary nodes.

Destinations: group related items into a zone, one chip each.

## 5. Re-point the paths

Compute border midpoints from the `style` values you just set. For a source node at
`left:66px; top:212px; width:236px` and height ~48px, the right-wall midpoint is
`(66+236, 212+24)` = `(302, 236)`.

Move the junction circle to the hub zone's right-wall midpoint.

## 6. Verify

```
[ ] scope renamed in CSS and JS, keyframes included
[ ] exactly one .primary
[ ] three hues
[ ] pauses offscreen
[ ] zero active elements under reduced motion
[ ] tooltips keyboard reachable
[ ] no console errors, no horizontal overflow
```

Headless check:

```js
await page.emulateMedia({ reducedMotion: 'reduce' });
const active = await page.evaluate(() => document.querySelectorAll('.hot,.lit').length);
// expect 0
```

## 7. Logos

Inline as base64. No external requests — these files get emailed around and opened
on machines with no access to your CDN.

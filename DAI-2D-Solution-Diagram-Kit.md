# Equinix Distributed AI — 2D Solution Diagram Kit

**Status:** Internal working spec · **Owner:** Saxton Randle-Sims (DAI PMM)
**Last updated:** 2026-07-28
**Applies to:** `Agentic Connectivity` and `DAI Hub` interactive 2D solution diagrams

This document is the complete specification for the animated 2D solution diagrams
built for Distributed AI. It exists so a new diagram can be produced without
re-deriving the visual grammar, and so a developer can rebuild these components in
a real stack without reverse-engineering the mockups.

Everything below is extracted from the shipped components, not from memory.

---

## 1. What these diagrams are

Two self-contained, dependency-free HTML/CSS/SVG components. No framework, no build
step, no external requests — logos are inlined as base64. Each drops into a page as
a single `<div>` with a scoped id and can be opened standalone from any machine.

| Diagram | Component id | Lives in | Logos |
|---|---|---|---|
| Agentic Connectivity | `#agx` | `ecosystem.html`, plus a standalone export | 15 |
| DAI Hub | `#dhx` | `hub.html` | 12 |

All CSS and JS are scoped under the component id. Nothing leaks to the host page.
This is a hard requirement — an earlier revision let a global `*`, `body` and `h1`
rule escape the `#agx` scope and it clobbered the host page shell.

---

## 2. Shared design system

### 2.1 Canvas

Both diagrams use an identical coordinate space:

```
viewBox = "0 0 1240 700"
```

Nodes, zones and tiles are absolutely positioned in that space. A `ResizeObserver`
drives a `fitScale()` routine that scales the whole stage to the container width,
so the diagram stays sharp at any size without re-layout.

### 2.2 Color tokens

```css
--canvas:      #0D1119;   /* diagram background            */
--ink:         #FFFFFF;   /* primary label text            */
--muted:       #8B93A7;   /* sub-label / caption text      */
--border:      #2C3548;   /* default node + zone border    */
--hair:        #2C3548;   /* hairlines                     */

--red:         #E91C24;   /* Equinix — the hub and its routes */
--red-tint:    #FBECEE;
--red-line:    #F2C6C9;

--blue:        #086AE3;   /* customer workloads / private infrastructure */
--blue-tint:   rgba(8,106,227,.05);
--blue-line:   #C3D8F5;

--violet:      #7739D9;   /* partner / policy sidecar      */
--violet-tint: rgba(119,57,217,.05);
--violet-line: #D9CBF2;
```

**The color rule is semantic, not decorative.** Blue is always the customer side.
Red is always Equinix and only Equinix. Violet is always the partner/security
sidecar. Do not introduce a fourth hue without changing this document first.

### 2.3 Typography

```css
font-family: NexaText, "Nexa Text", Arial, sans-serif;
line-height: 1.4;
-webkit-font-smoothing: antialiased;
```

| Element | Size | Weight | Treatment |
|---|---|---|---|
| Node title (`b`) | 11.5px | 600 | `--ink` |
| Node sub-label (`span`) | 7px | 600 | uppercase, `.1em` tracking, `--muted` |
| Primary node title | 12.5px | 600 | white on red |
| Logo tile label (`b`) | 10px | 600 | `#131925`, `nowrap` |

### 2.4 The visual grammar

Read left to right, always:

```
CUSTOMER SIDE          →   EQUINIX HUB          →   ECOSYSTEM
(blue, left)               (red, center)            (neutral tiles, right)
                                 ↓
                        PARTNER SIDECAR
                        (violet, dashed, below)
```

The hub is the only saturated element. Everything else is subordinate to it. If a
viewer cannot tell within a few seconds that Equinix is what connects the
architecture, the diagram has failed.

### 2.5 Zone containers

```css
.zone            { border-radius:12px; border:1px solid var(--border); }
.zone.z-neutral  { background:#131925; border-color:#2C3548; }
.zone.z-white    { background:#151C29; border-color:#2C3548; }   /* ecosystem groups */
.zone.z-side     { background:rgba(119,57,217,.05);
                   border:1px dashed var(--violet-line); border-radius:10px; }
```

The Equinix hub zone is the one exception — it carries a red tint, a red border and
a solid red left rail:

```css
.zone.z-hub, .zone.z-red {
  background:   rgba(233,28,36,.10);
  border-color: rgba(233,28,36,.65);
  border-left:  3px solid #E91C24;
}
```

### 2.6 Nodes

```css
.node        { background:#151C29; border-color:#2C3548; border-radius:8px; }
.node.lit    { border-color:var(--red); background:#241016; }
.node.primary{ background:#E91C24; border-color:#E91C24;
               box-shadow:0 0 0 1px rgba(233,28,36,.45); }
.node.primary.lit { box-shadow:0 0 0 4px rgba(233,28,36,.28); }
```

Exactly **one** `.primary` node per diagram — the persistent solid-red hub anchor.
`Equinix Fabric®` in Agentic Connectivity, `Equinix Fabric® Suite` in the DAI Hub.

### 2.7 Logo tiles

```css
.tile   { height:32px; background:#ECEEF1; border:1px solid #D3D7DE;
          border-radius:8px; display:flex; align-items:center;
          padding:0 10px; gap:7px; }
.tile b { font-size:10px; font-weight:600; color:#131925; white-space:nowrap; }
```

Tiles are deliberately **off-white `#ECEEF1`, not pure white**. Pure white tiles
out-shouted the Fabric hub and pulled the eye to the right side of the canvas.

---

## 3. Connector system

Paths are SVG, colored by a `data-c` attribute: `b` = blue, `r` = red, `v` = violet.

### Agentic Connectivity (`#agx`)

| State | Stroke | Opacity |
|---|---|---|
| Base path (all) | 1.75px | .55 |
| Red idle | 2px | .72 |
| Red active (`.hot`) | 3px | 1 |
| Any active (`.hot`) | 2.25px | 1 |

Violet uses `stroke-dasharray:5 5`. Transitions run at `.4s` on opacity and
stroke-width. An 8px red junction port sits at the fan-out origin (`780,368`) so
the eye gets a clear point of departure.

### DAI Hub (`#dhx`)

| Path | Idle | Active (`.hot`) |
|---|---|---|
| Blue | 2px / .6 | 3px, marching dash `dhxdash 1.15s linear infinite` |
| Red | 2px / .72 | 3px, marching dash `dhxdash 1.05s linear infinite` |
| Violet | 1.75px / .55, dash `5 5` | `dhxdashv 1.35s linear infinite` |

Active dash pattern is `7 9`. The DAI Hub diagram uses marching-dash motion; the
Agentic Connectivity diagram uses opacity/width pulsing. Both are valid within the
system — pick one per diagram and stay consistent.

---

## 4. Animation choreography

The loop tells the story rather than decorating it. Sequence:

1. A blue request leaves the customer/private-infrastructure zone
2. The hub zone border brightens
3. The primary red node intensifies
4. One red route fires toward a rotating destination group
5. The destination group brightens
6. Security/policy partners flicker on the violet sidecar
7. The response returns, then the loop pauses before repeating

Destinations rotate: foundation model APIs → neoclouds → cloud service providers.

**Implementation hooks.** `#agx` exposes `start()`, `stop()`, `cycle()`, `arrive()`,
`pulse()`, `hotPath()`, `hotZone()`, `sideActivate()`, `drawPaths()`, `fitScale()`,
`evaluate()`, `later()`. `#dhx` exposes `cycle()`, `hot()`, `lit()`, `clearAll()`,
`stop()`, `later()`, `evaluate()`.

---

## 5. Interaction

### Hover definitions — DAI Hub only

The DAI Hub diagram carries capability definitions as hover tooltips on the
diagram itself, replacing what was previously a nine-card section below it.

```css
.node.has-tip                        { cursor:help; }
.node.has-tip:hover                  { border-color:var(--red);
                                       box-shadow:0 0 0 3px rgba(233,28,36,.14);
                                       z-index:60; }
.node.has-tip:focus-visible          { outline:2px solid var(--red); outline-offset:2px; }
.node.has-tip:hover .tip,
.node.has-tip:focus-within .tip      { opacity:1; visibility:visible; }
```

Seven nodes carry `.has-tip` with seven matching tooltip bodies, plus the hint line
"Hover any Hub capability to see what it does."

**Accessibility requirement:** tooltips open on `:focus-within` as well as `:hover`,
so they are reachable by keyboard. Definitions must never be the *only* place
essential information lives.

---

## 6. Responsive and accessibility

- **Scaling** — `ResizeObserver` → `fitScale()`. The 1240×700 canvas scales to the
  container. No re-layout, no reflow.
- **Mobile** — the diagram stacks vertically below the mobile breakpoint.
- **Offscreen pausing** — `IntersectionObserver` stops the animation loop when the
  diagram scrolls out of view. Do not ship a version that animates offscreen.
- **Reduced motion** — under `prefers-reduced-motion: reduce`, all transitions and
  animations are hard-disabled and paths settle to a legible static state:

```css
@media (prefers-reduced-motion: reduce) {
  .svg path, .zone, .node, .tile { transition:none!important; animation:none!important; }
  .svg path { opacity:.8; }   /* #agx — .85 in #dhx */
  .tile     { opacity:1; transform:none; }
}
```

---

## 7. Content inventories

### Agentic Connectivity (`#agx`)

**Customer side (blue)** — Apps and AI agents: AI agents · Copilots and applications ·
Agent orchestration. Private data and infrastructure: Enterprise data and systems of
record · Enterprise APIs and tools · AI-ready compute and storage.

**Equinix Fabric® Suite (red)** — Equinix Fabric® *(primary)* · Fabric Cloud Router ·
Network Edge · Internet Access · Internet Exchange®.

**Ecosystem (right)** — Foundation model APIs: OpenAI · Anthropic · Gemini.
Neoclouds · AI compute: CoreWeave · Crusoe · Groq · Nebius.
Cloud service providers: AWS · Azure · Google Cloud · Oracle Cloud.
Network service providers: Verizon · AT&T · Telstra.

**Partner sidecar (violet)** — Palo Alto · Prisma AIRS · Pangea · F5.

### DAI Hub (`#dhx`)

**Private colocation · managed private cloud (blue)** — Private databases & models ·
GPUs · LPUs · CPUs · RAM · TB storage · Liquid cooling.

**Equinix Distributed AI™ Hub (red)** — Equinix Fabric® Suite *(primary)* ·
AI Gateways · AI Security Guardrails · AI Data Storage · AI Observability &
Resilience · Orchestration & Compliance · AI Optimization.

**Equinix Interconnection** — Equinix Fabric® · Fabric Cloud Router · Network Edge ·
Internet Access · Internet Exchange®.

**Ecosystem** — Foundation model APIs: OpenAI · Anthropic · Gemini.
Neoclouds · AI compute: Groq · Crusoe · CoreWeave · Nebius.
Cloud service providers: AWS · Azure · Google Cloud · Oracle Cloud.

**Partner sidecar (violet)** — Palo Alto · Prisma AIRS.

---

## 8. Building a new diagram

1. Copy the nearest existing component and rename the scope id (`#agx` → `#xyz`).
   Rename **every** selector — a partial rename is how scope leaks happen.
2. Keep the 1240×700 canvas. Do not invent a new coordinate space.
3. Keep the three-color semantic rule. Blue customer, red Equinix, violet partner.
4. Place exactly one `.primary` node.
5. Lay out left → center → right, sidecar below.
6. Swap node labels and logo tiles. Tiles are base64 — no external requests.
7. Re-point the connector paths and the fan-out junction.
8. Keep `ResizeObserver`, `IntersectionObserver` and the reduced-motion block.
9. Verify: scope leak, mobile stack, offscreen pause, reduced motion, keyboard focus.

**Next evolution, not yet built:** the diagram is data-positioned, so it could be
driven by a JSON config (`{ leftNodes:[…], hub:{…}, ecosystem:[…] }`) that renders
the SVG. That turns "make a diagram for a new use case" into editing a config
rather than moving SVG paths. Worth doing if more than two or three more diagrams
are expected.

---

## 9. Clearance — read before any external use

These diagrams are **internal review assets**. Several elements are not cleared:

- **Partner logos and names.** Every logo needs partner-marketing sign-off. NVIDIA
  references additionally require NVIDIA approval. Logo rights and case-study
  clearance are separate approval tracks.
- **"Equinix Distributed AI™ Hub"** and **"Fabric Intelligence™"** naming are
  uncleared. The TDN™ precedent applies — TDN™ and "AI Cloud Exchange" / "AI
  Exchange" are explicitly not cleared for external web.
- **Nebius** appears in the ecosystem tiles but is on the "Not Yet Published" list
  in the claims library. Confirm status before external use.
- Official Equinix solution diagrams live in the DAI diagram deck and the brand
  kit's `04_Technical_Diagrams`. **These components are not a substitute for an
  official brand-team asset.** For anything customer-facing, the animated web
  version and the static brand-kit version should trace to one canonical topology
  so they cannot drift.

---

## 10. Handoff note for engineering

Treat these files as a **reference implementation and design spec, not production
code**. Every color, dimension, timing and label is pinned down here, which removes
the interpretation gap a Figma handoff usually leaves. But a real deployment will
rebuild the markup inside the actual stack — CMS, design-system components,
licensed NexaText webfonts, analytics, accessibility audit, localization.

What should travel is the design decisions in this document. What should not travel
verbatim is the HTML.

---

## Appendix — known issues

- **Fixed:** an earlier `#agx` revision leaked global `*`, `body` and `h1` rules
  outside its scope and broke the host page shell. Scoped typography now lives on
  `#agx` itself. Re-check this on any copy-and-rename.
- **Open:** the Agentic Connectivity animation choreography (blue arrives → border
  brightens → node intensifies → route fires) was specified but the full sequenced
  version was never implemented, because it required editing working animation JS
  that could not be visually verified at the time. The static hierarchy carries the
  message; the sequenced pulse is a targeted follow-up.
- **Note:** both `ecosystem.html` and `hub.html` embed their logos as base64 (15 and
  12 images respectively). Any claim that one of them is missing logos is a grep
  artifact — the two components use different tile class names.

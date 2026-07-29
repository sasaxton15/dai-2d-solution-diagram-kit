---
name: 2d-solution-diagram
description: Build or review an animated 2D architecture / solution diagram in HTML+CSS+SVG. Use when asked to create a solution diagram, architecture diagram, connectivity diagram, marketecture, topology diagram, or "diagram showing how X connects to Y"; when asked to animate an existing diagram; or when reviewing a diagram for visual hierarchy, colour semantics, accessibility or scope leakage. Triggers on "solution diagram", "architecture diagram", "connectivity diagram", "topology", "marketecture", "diagram the flow", "animate the diagram", "review this diagram".
---

# 2D Solution Diagram

Build animated architecture diagrams that read correctly in seconds. Fork the
template; do not start from a blank file.

## Before writing any code

1. Read `README.md` for the method.
2. Copy `template/diagram-template.html` — it already implements the whole contract.
3. Identify the three lanes for **this** diagram: what is the source, what is the
   hub, what are the destinations. If you cannot name a single hub, the diagram
   is not ready to draw yet — resolve that first.

## Non-negotiables

- **One canvas.** `viewBox="0 0 1240 700"`, absolutely positioned nodes, scaled by
  `ResizeObserver`. Never invent a new coordinate space.
- **Three lanes, left to right.** Source → hub → destinations, sidecar below.
- **Three hues, semantic.** One per lane. Never a fourth. Colour means role, not mood.
- **Exactly one `.primary` node** — the saturated hub anchor. Everything else is a
  dark node with a hairline border.
- **Off-white destination chips**, never pure white. Pure white steals focus from the hub.
- **Animation is narrative.** Request → hub acknowledges → hub intensifies → one
  route fires → destination brightens → sidecar checks → return → pause. Rotate the
  destination each cycle. Never fire every route at once.
- **Scope everything under one id.** Rename it completely when forking — CSS *and*
  JS. A partial rename leaks styles into the host page.
- **Accessibility contract.** `IntersectionObserver` pauses offscreen;
  `prefers-reduced-motion` hard-disables all motion; tooltips open on
  `:focus-within` as well as `:hover`; essential content never lives in a tooltip alone.

## Building

1. Copy the template, rename the scope everywhere.
2. Replace the three hues in `:root`. Keep the role names.
3. Swap labels: zone titles, node titles and sub-labels, destination chips.
4. Re-point the SVG paths. Anchor to container borders, not to inner elements.
   Move the fan-out junction circle to match the new origin.
5. Inline any logos as base64. No external requests — these files must open from
   any machine with no network.

## Reviewing an existing diagram

Score against these, in order of how badly each one hurts:

1. Can you identify the hub in under three seconds? If not, something is competing
   with it — usually bright chips or a second saturated element.
2. Is colour semantic and limited to three hues?
3. Does the animation narrate a path, or just shimmer?
4. Does it stop offscreen and under reduced motion?
5. Is anything essential hover-only?
6. Do any global rules escape the component scope?

## Verify before shipping

Render headless and assert: exactly one `.primary`; zero console errors; no
horizontal overflow; zero active elements under `prefers-reduced-motion`; the stage
transform scales with container width.

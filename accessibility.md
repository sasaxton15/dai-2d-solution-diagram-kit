# Accessibility and responsive contract

Three requirements. All are implemented in the template. None are optional.

## 1. Stop offscreen

```js
new IntersectionObserver(e => e[0].isIntersecting ? start() : stop(),
                         { threshold: 0.15 }).observe(root);
```

An animation loop running in a page the user has scrolled past is wasted battery and
CPU. `stop()` must also clear pending timers, not just stop scheduling new ones.

## 2. Honour reduced motion

```css
@media (prefers-reduced-motion: reduce){
  .svg path, .zone, .node, .tile { transition:none!important; animation:none!important; }
  .svg path { opacity:.85; }
}
```

And in JS — the loop must not start at all:

```js
const reduce = matchMedia('(prefers-reduced-motion: reduce)');
function start(){ if (running || reduce.matches) return; running = true; cycle(); }
reduce.addEventListener('change', () => reduce.matches ? stop() : start());
```

CSS alone is not enough. Without the JS guard the loop still toggles classes; it just
does so invisibly, which is a state machine running for nothing.

**Verify it.** Render with reduced motion forced and assert zero elements carry
`.hot` or `.lit` after the loop would normally have fired.

## 3. Keyboard reachable

```css
.node.has-tip:focus-visible          { outline:2px solid var(--hub); outline-offset:2px; }
.node.has-tip:hover      .tip,
.node.has-tip:focus-within .tip      { opacity:1; visibility:visible; }
```

Tooltip nodes need `tabindex="0"` and the tooltip needs `role="tooltip"`.

**Essential information must never be hover-only.** A definition that exists only in
a tooltip is invisible to touch users, screen readers in some modes, and anyone
printing the page. If it matters, it belongs in the surrounding copy too.

## Responsive

Scaling the stage rather than re-laying-out means there is no mobile breakpoint
inside the diagram — it shrinks proportionally. Below roughly 600px container width
the labels get small; at that point stack the diagram's host section vertically, or
offer the diagram as a tap-to-expand.

Check for horizontal overflow at every width you support. The stage is a fixed
1240px element and will overflow if the scale transform isn't applied.

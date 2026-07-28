# Equinix Distributed AI — Website Phase 2

Internal working repository for the Distributed AI website Phase 2 mockups and the
2D solution diagram system.

> ## ⚠ PRIVATE — do not make this repository public
>
> This repository contains Equinix pre-release material that is **not cleared for
> external distribution**:
>
> - **Partner logos and names** — every logo requires partner-marketing sign-off.
>   NVIDIA references additionally require NVIDIA approval. Logo rights and
>   case-study clearance are separate approval tracks.
> - **Uncleared product naming** — "Equinix Distributed AI™ Hub" and
>   "Fabric Intelligence™" are not cleared. The TDN™ precedent applies; TDN™ and
>   "AI Cloud Exchange" / "AI Exchange" are explicitly not cleared for external web.
> - **Nebius** appears in ecosystem tiles but is on the "Not Yet Published" list in
>   the claims library.
> - **Unverified statistics** — AiHPC's 22× figure is unverified. "World's most
>   expansive" superlatives and the IDC citation need checking before any external use.
>
> Making this repository public publishes all of the above. Share by collaborator
> invite only.

---

## What's in here

### Documentation

| File | What it is |
|---|---|
| `DAI-2D-Solution-Diagram-Kit.md` | Complete specification for the two animated 2D solution diagrams — tokens, grammar, connector system, animation choreography, accessibility contract, content inventories, and a procedure for building a new one. **Start here.** |

### Page mockups

Single-file HTML. Inline CSS, base64 images, no build step, no dependencies — each
opens from any machine.

| File | Page |
|---|---|
| `index.html` | Main Distributed AI page reconstruction |
| `hub.html` | DAI Hub — contains the `#dhx` solution diagram |
| `ecosystem.html` | Connect your data, models and AI ecosystem — contains the `#agx` solution diagram |
| `inference.html` | Inference at the metro edge |
| `data-center.html` | Give your AI the data center it needs |
| `sovereignty.html` | Maintain privacy and sovereignty |
| `cost.html` | Cost optimization and token economics |
| `dai-homepage.html` | Inference Factory announcement placement study |

### The two solution diagrams

Both live inside their host pages as scoped components:

- **Agentic Connectivity** — `#agx`, inside `ecosystem.html`. 15 embedded logos.
- **DAI Hub** — `#dhx`, inside `hub.html`. 12 embedded logos, plus hover
  definitions on seven capability nodes.

Both use an identical `viewBox="0 0 1240 700"` canvas and the same semantic color
rule: **blue is the customer side, red is Equinix and only Equinix, violet is the
partner sidecar.** Full spec in `DAI-2D-Solution-Diagram-Kit.md`.

---

## Deployment

Deploys to Vercel as a static site. There is no `package.json`, no build step and no
dependencies — Vercel uploads the files as-is.

```bash
vercel --prod
```

`.vercel/` is gitignored.

---

## For engineering

Treat these files as a **reference implementation and design spec, not production
code**. Every color, dimension, timing and label is pinned down, which removes the
interpretation gap a design handoff usually leaves. A real deployment should rebuild
the markup inside the actual stack — CMS, design-system components, licensed
NexaText webfonts, analytics, accessibility audit, localization.

What should travel is the design decisions. What should not travel verbatim is the HTML.

**Known asset gap:** no NexaText font files are bundled. The font stack is declared
correctly but falls back to Noto Sans / system UI outside an Equinix machine, so
letterfit differs slightly from production.

---

## Owner

Saxton Randle-Sims — Distributed AI PMM

# Generative Still Life

A fractal bouquet that grows a new arrangement on every refresh.

**[See it live →](https://smika6.github.io/fractal-bouquet/)**

Each load seeds a fresh bouquet — species, palette, and composition — then draws it
to a canvas. The seed lives in the URL hash, so any arrangement you like can be
linked, reloaded, and shared exactly as it was.

## The fractals are the botany

The recursion isn't decoration layered on top of the flowers; it's how they're built.

- **Baby's breath** is recursive binary branching, terminating in five-petal florets.
- **Ferns** are bipinnate — every leaflet on a frond is itself a smaller frond.
- **Roses and ranunculus** lay their petals on the golden angle (137.5°), the same
  phyllotaxis real flowers use.
- **Daisy seed-discs** pack florets on a Fermat spiral, so the counter-rotating arms
  emerge on their own rather than being drawn in.

## Making them read as real flowers

Every petal comes from one shared outline with a bluntness parameter, so a lily tepal
tapers to a point while a rose petal arrives at its tip almost horizontally. That single
control does most of the work of separating the species by silhouette.

From there each species gets what actually distinguishes it: lilies have recurved tepals
in two whorls, freckled throats, and six pollen-dusted anthers; tulips are cupped goblets
with three tepals hugging three behind; anemones get their black boss; poppies get the
dark basal ring.

Three details mattered more than expected, and each was only found by rendering and looking:

- **Petal tips.** Everything tapered to a point at first, which made roses read as spiky
  dahlias. Rebuilding the tip geometry fixed the whole cast at once.
- **Petal edges.** Pale bouquets merged into a single blob, because overlapping cream
  petals had nothing separating them. Real petals cast an edge, so each carries a subtle
  darker rim, weighted stronger on pale blooms.
- **Palette.** Petal hues are snapped to bands that actually occur in flowers — no lime
  blooms — with one dominant hue carrying ~62% of the bouquet and a second as accent, the
  way a florist builds. A triad of pure primaries is technically harmonious and still
  reads as a circus.

The placard names what actually bloomed: cultivars are chosen from each flower's rendered
colour, so a blue rose is labelled 'Blue Moon' and a near-black tulip 'Queen of Night'.

## Controls

| Action | |
| --- | --- |
| New bouquet | Refresh, press `space`, or click **Regenerate** |
| Save a PNG | Press `s`, or click **Save** |
| Keep one | Copy the URL — the `#s=` seed reproduces it exactly |

## Running it

One self-contained file with no dependencies, no build step, and no network calls.
Open `index.html` in a browser, or serve the folder:

```bash
python -m http.server 8000
```

## Notes

Drawing is seeded per frame, so a given bouquet renders identically every time rather
than shimmering as it animates in. The canvas guards against a zero-sized viewport and
recovers via `ResizeObserver`, so it still renders when embedded in a frame that starts
hidden. Motion respects `prefers-reduced-motion`.

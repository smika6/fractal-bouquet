# Generative Still Life

A fractal bouquet that grows a new arrangement on every refresh.

**[See it live →](https://smika6.github.io/fractal-bouquet/)**

Each load seeds a fresh bouquet — species, palette, vase, and composition — then draws
it to a canvas. The seed lives in the URL hash, so any arrangement you like can be
linked, reloaded, and shared exactly as it was.

## Make your own

Press **Make your own** (or `e`) to open the arranging table:

- **Add flowers and greenery** from the pickers — the thumbnails are the real thing,
  drawn live in whichever colour you've selected.
- **Drag anything** to move it, and turn it with the brass grip that follows it around.
  Flowers, ferns, eucalyptus and gypsophila are all the same kind of object: every one
  is stemmed into the vase and measured from it, so the arrangement holds together at
  any window size.
- **Reorder the layers.** The layer list shows the whole stack, front at the top. Move a
  stem one step or send it to the very front or back, so greenery can sit behind the
  flowers or spill in front of them.
- **Choose a vase** — compote, bud vase, ginger jar, jar, cylinder, urn, goblet, or a
  plain hand-tied twine wrap. Glass vessels show the stems through the water.
- **Empty the vase** to start from nothing and build it yourself.

Hand-arranged bouquets are encoded into the URL as you work, so a refresh doesn't lose
them and the link carries the whole arrangement to whoever you send it to.

| | |
| --- | --- |
| Select / move | Click anything, then drag |
| Turn | Drag the brass grip, or `[` and `]` |
| Nudge | Arrow keys (`shift` for bigger steps) |
| Layer | `PageUp` / `PageDown`, or the Front / Back buttons |
| Remove | `Delete` |

Foliage is hit-tested against its own alpha mask rather than a bounding box, so a click
lands on a frond only where leaves actually are and falls through the gaps to whatever
is behind. Anything buried completely behind the flowers is still reachable from the
layer list.

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
  reads as a circus. Yellows are additionally floored in lightness, because a dark yellow
  is olive, and no flower is olive.

Vessels are half-silhouette profiles, mirrored and smoothed. The opening is drawn in two
passes — the far rim behind the stems, the body in front of them — because filling the
whole mouth on top makes a bowl read as a plate laid over the flowers. Glass takes a light
fill and hard-edged highlights so the form comes from its edges; ceramic gets a matte
falloff, since the same curve makes stoneware look chromed.

The placard names what actually bloomed: cultivars are chosen from each flower's rendered
colour, so a blue rose is labelled 'Blue Moon' and a near-black tulip 'Queen of Night'.

## Controls

| Action | |
| --- | --- |
| New bouquet | Refresh, press `space`, or click **Regenerate** |
| Save a PNG | Press `s`, or click **Save** |
| Keep one | Copy the URL — the `#s=` seed reproduces it exactly |

## As a desktop wallpaper

There is a [Wallpaper Engine build](wallpaper-engine/) in this repo. Wallpaper Engine's
Web wallpaper type runs a local HTML file in an embedded Chromium, so the same single
file works there with no changes — it detects the host, hides the editing chrome, and
cycles bouquets on a timer. It idles at zero CPU between them.

## Running it

One self-contained file with no dependencies, no build step, and no network calls.
Open `index.html` in a browser, or serve the folder:

```bash
python -m http.server 8000
```

## Pollinators

A bee, a butterfly and a dragonfly work the bouquet, each flying in character — the bee
darts and fusses, the butterfly drifts, the dragonfly hangs still and then crosses the
frame in a blink. Move the cursor near one and it gets distracted, breaking off to circle
the pointer until it loses interest. A stationary cursor is ignored; it has to be moving
to be worth chasing. Press `b` to send them away.

They are close to free. The settled bouquet is captured once, and each frame repaints only
the small square around each insect out of that capture — about 1.6% of the screen and
0.1ms a frame, against 15ms to redraw the arrangement. The flowers never redraw while the
insects fly, which is what lets this run on a desktop without costing anything.

## How it draws

Building a bouquet is essentially free — a few hundred microseconds of geometry.
Painting one is not: a couple of dozen flowers is thousands of filled paths, around
10ms a frame at retina resolution. Animating that directly meant redrawing every petal
seventy times over, which is what made the old intro stutter.

So the arrangement is painted **once**, into two offscreen layers — the flowers and the
vessel — while the previous bouquet is still on screen. The reveal then composites those
layers: the vessel settles, the bouquet opens out of its mouth past its final size and
eases back, and the bouquet you were looking at dissolves away underneath. Two blits a
frame, under 2ms, so the curve can be as lively as it likes. The layers are released as
soon as the reveal ends, and the settled frame is always drawn live, at full resolution.

Fronds are baked to sprites the same way, since a bipinnate fern is thousands of leaf
blades and only changes when you edit it.

## Notes

Drawing is seeded per frame, so a given bouquet renders identically every time rather
than shimmering as it animates in. The canvas guards against a zero-sized viewport and
recovers via `ResizeObserver`, so it still renders when embedded in a frame that starts
hidden. Motion respects `prefers-reduced-motion`.

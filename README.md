# Generative Still Life

A fractal bouquet that grows a new arrangement on every refresh.

**[See it live →](https://smika6.github.io/fractal-bouquet/)**

Each load seeds a fresh bouquet — species, palette, vase, and composition — then draws
it to a canvas. The seed lives in the URL hash, so any arrangement you like can be
linked, reloaded, and shared exactly as it was.

Twenty-one flowers, nine kinds of greenery, a vessel, and now and then something on the
table: a petal that has dropped, or a stem that never made it into the vase.

## Make your own

Press **Make your own** (or `e`) to open the arranging table:

- **Add flowers, greenery and table pieces** from the pickers — the thumbnails are the
  real thing, drawn live in whichever colour you've selected. Each species keeps to the
  colours it actually comes in, so picking coral for lavender still gives you lavender.
- **Drag anything** to move it, and turn it with the brass grip that follows it around.
  Flowers, fronds, sprigs and spikes are all the same kind of object: every one is
  stemmed into the vase and measured from it, so the arrangement holds together at any
  window size. Ivy and amaranthus hang from the rim; petals and cut stems lie on the
  table and stay on it whichever vessel you move to.
- **Resize** with the scroll wheel, `-` and `+`, or the size slider.
- **Tilt a flower** to turn its face away; its sepals show on the stem side. Turned far
  enough, a tulip shows its goblet in profile. **Make it a bud** (`o`) closes it up.
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
| Resize | Scroll wheel, or `-` and `+` |
| Bud / open | `o` |
| Undo / redo | `ctrl+z` / `ctrl+shift+z` (or `ctrl+y`) |
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
- **Daisy and sunflower seed-discs** pack florets on a Fermat spiral, so the
  counter-rotating arms emerge on their own rather than being drawn in.
- **Queen Anne's lace** is a compound umbel — an umbel of umbels — so it is one function
  called at two depths, with the single dark floret real ones carry at the heart.
- **Pompon dahlias, hydrangeas, alliums, marigolds and 'Teddy Bear' sunflowers** are
  the golden angle made solid: quills, florets, stars, frills and shaggy rays are all set
  out on the same spiral. A Teddy Bear is a sunflower whose seed-disc has turned
  entirely to petals, so it is the sunflower's own spiral with nothing left to show.
- **Spikes** — delphinium, snapdragon, foxglove, lavender — set their florets round the
  stalk on the golden angle too, and draw the far side first, in shadow.
- **Blossom branches** fork recursively, blossoms crowding the young wood. **Amaranthus**
  is a chenille rope that branches as it falls.

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

A bouquet is built the way a florist builds one, in roles. A hero and one or two
supporting flowers make the body; spikes rise through the back to give it line; a few
airy accents — lace, alliums — stand out above it on their own stems; and texture
(hypericum berries, wheat, pampas, blossom) threads through. What stops it reading as a
sheet of stickers is that not every flower faces you:

- **Heads turn.** Blooms on the rim of the dome foreshorten along the line they lean out
  on, and show their sepals on the stem side. A tulip turned far enough becomes a goblet
  in profile, which a squashed star never reads as.
- **Some haven't opened.** Each species has its own bud — a furled rose, a green-based
  tulip egg, a long blushed lily, a nodding hairy poppy — set out at the edges.
- **Stems carry leaves**, and each species grows its own: compound leaves on a rose, a
  strap on a tulip, narrow blades on a lily.
- **Something spills.** Ivy and amaranthus hang over the rim, falling under a gravity
  that knows which way the floor is however the stem is turned.
- **Something has dropped.** Sometimes a petal lies on the table, sometimes a whole
  stem, its head resting on its rim.

Each species also only accepts the colours it really comes in. The palette proposes;
lavender stays lavender, a sunflower in a pink bouquet becomes 'Velvet Queen' burgundy,
and nothing gets a true blue it doesn't have.

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
| Step through recent | `←` and `→`, or the browser's Back and Forward |
| Save a PNG | `s` for this screen, `shift+s` for 4K, or **Save** for 4K, phone and square sizes |
| Keep one | **Keep** (`f`) puts it in the gallery; **Gallery** (`g`) shows kept and recent bouquets |
| Share one | Copy the URL — the `#s=` seed reproduces it exactly, and a pasted link loads in an open tab |
| Pollinators | `b` sends them away, or brings them back |

Larger saves are painted fresh at that size rather than scaled up from the screen, and
fronds are drawn as vectors for them instead of from their screen-resolution sprites.
The gallery lives in this browser's storage, with a small picture of each bouquet.

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

A bee, a butterfly, a dragonfly, a hummingbird and a ladybird work the bouquet, each in
character — the bee darts and fusses, the butterfly drifts, the dragonfly hangs still and
then crosses the frame in a blink. Bees and butterflies settle on flowers for a while,
and a settled butterfly slowly opens and closes its wings. The hummingbird hovers with
its bill in the spikes and trumpets it can drink from. The ladybird walks: it ambles over
one flower, pausing and turning, until it has had enough or the cursor comes too close,
and only then parts its wing cases and flies to another. Press `b` to send them away.

Move the cursor near one and it locks on, then **travels with you** — it flies at the
cursor's own speed rather than chasing the spot it just left, holding a loose station off
to one side. Nothing times out: it keeps company for as long as it can physically hold on,
and you break the lock by **outrunning it**. Amble and the fliers stay with you; move
briskly and the butterfly drops away; swipe hard and you shake the lot. Which one you can
lose falls out of their real top speeds rather than any rule — the dragonfly is very hard
to leave behind, and if you flick and then stop, it simply catches up again.

They are close to free. The settled bouquet is captured once, and each frame repaints only
the small square around each insect out of that capture — about 2% of the screen and
0.1ms a frame, against 15–20ms to redraw the arrangement. The flowers never redraw while the
insects fly, which is what lets this run on a desktop without costing anything.

## How it draws

Building a bouquet is essentially free — a few hundred microseconds of geometry.
Painting one is not: a couple of dozen flowers is thousands of filled paths, around
10–20ms a frame at retina resolution. Animating that directly meant redrawing every petal
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

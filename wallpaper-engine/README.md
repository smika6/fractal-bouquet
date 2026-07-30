# Generative Still Life — Wallpaper Engine

A [Wallpaper Engine](https://store.steampowered.com/app/431960/) build of the fractal
bouquet. Wallpaper Engine's **Web** wallpaper type runs a local HTML file in an embedded
Chromium, and this project is one self-contained file — no dependencies, no build step,
no network access — so it ports across almost unchanged.

It also **idles at zero CPU**: once a bouquet finishes arriving, the render loop stops
entirely until the next one is due. Nothing animates in between.

## Local install vs. publishing

Two different things, and you only need the first if you want the second:

- **Loading it in** (below) puts it on your own machine. Nobody else can see it.
- **Publishing to the Steam Workshop** hosts it for everyone. Steam stores and
  distributes the files; you are not hosting anything. Others subscribe and it downloads
  automatically, updates included.

Publishing happens *from* the editor, so the local install is simply the one-time step
that gets the project in front of the publish button.

## Installing it

1. Open Wallpaper Engine → **Wallpaper Editor** → **Create Wallpaper**.
2. Pick any file when prompted, choose **Web**, and give it a name. This creates a
   project folder under `Steam/steamapps/common/wallpaper_engine/projects/myprojects/`.
3. Copy `index.html`, `preview.png` and `project.json` from this folder into that
   project folder, replacing what the editor generated.
4. Reopen the project in the editor. The preview should show the bouquet, and the five
   settings below should appear in the properties panel.
5. **Apply** it, or use **Workshop → Publish** to upload it (set `visibility` in
   `project.json` to `public` first if you want it listed).

If you would rather not hand-edit the manifest, the editor's own
*Edit → Change project settings → Add property* dialog produces the same result — the
property names must match `cycleseconds`, `vessel`, `fullness`, `showplacard`, `motion`.

## Publishing it to the Workshop

With the project open in the editor, use **Share on Workshop**. Only a **title** and a
**preview image** are strictly required; both are already here. In the publish dialog you
choose the visibility — `project.json` ships as `private`, so change it there (or in the
file) if you want it publicly listed.

`preview.jpg` is 1920×1080, the aspect the Workshop grid expects. If you replace it with
an animated GIF, keep it under 1MB and ideally under 500KB — that limit applies to
animated previews, not static ones.

The whole wallpaper is three small files and loads nothing from the network, which keeps
it well clear of the usual review friction around web wallpapers fetching remote content.

## Settings

| Setting | Default | What it does |
| --- | --- | --- |
| Seconds between bouquets | 45 | How often it rearranges. **0 holds one bouquet forever.** |
| Vessel | Surprise me | Pin one of the eight vessels, or let each bouquet pick. |
| Fullness | 100 | Flowers per bouquet, from a sparse handful to a crowded one. |
| Show the gallery placard | on | The museum label naming what bloomed. |
| Animate each new bouquet | on | Off swaps bouquets instantly, with no reveal. |
| Pollinators | 3 | A bee, a butterfly and a dragonfly, in that order. 0 for none. |

### The pollinators

They work the bouquet on their own errands — visiting flower heads, hovering, moving
on. Each flies in character: the bee darts and fusses, the butterfly drifts and bobs,
the dragonfly holds still then crosses the frame in a blink.

Move your cursor near one and it gets distracted, breaking off to circle the pointer
until it loses interest and goes back to the flowers. A *stationary* cursor is ignored —
it has to be moving to be worth chasing, so a parked mouse doesn't hold them hostage.

They cost almost nothing. The settled bouquet is captured once and each frame repaints
only the small square around each insect from that capture — around 1.6% of the screen,
roughly 0.1ms a frame, against 15ms to redraw the arrangement. The flowers themselves
are never redrawn while the insects fly.

## Notes on the port

The same `index.html` serves as both the web page and the wallpaper. It detects
Wallpaper Engine by the presence of its injected globals (or a `?mode=wallpaper` flag,
which is handy for testing in an ordinary browser) and then:

- hides the arranging studio, buttons and hints — there is nothing to click on a desktop
- cycles bouquets on a timer, since nobody refreshes a wallpaper
- skips writing the seed to the URL, which an embedded browser may refuse on `file://`
- pauses the cycle while the desktop is covered, so a fullscreen game costs nothing

Because the bouquet is painted once into offscreen layers and revealed by compositing,
each reveal frame costs under 2ms even at 4K — the work happens in a single ~26ms burst
between bouquets, not continuously.

## Interactivity

The arranging studio is deliberately switched off here. Wallpaper Engine can forward
mouse input, but a desktop is a poor place to drag flowers around — build your bouquet
in the browser version, and the URL it produces carries the whole arrangement.

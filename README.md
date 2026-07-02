# kotoba-lang/sprite2d

Kotoba runtime package for `kotoba.sprite2d`.

## Test

```sh
clojure -M:test
```

## Modules

### `kotoba.sprite2d` / `kotoba.sprite2d.layout` (existing)

A CLJS-only Canvas2D "hiccup for sprites" vector-shape character painter: EDN primitives
(`:ellipse`/`:circle`/`:rect`/`:arc`) composed into hand-authored character art, with
per-shape declarative animation (`:anim {:rot ... :pulse ... :bob ... :sway ...}`).
`kotoba.sprite2d.layout` is the pure (JVM-testable) camera/depth-sort/world→screen layout
companion — it turns a scene + entity snapshot into an ordered, screen-space draw list without
touching a canvas.

### `kotoba.sprite2d.atlas` / `kotoba.sprite2d.animation` / `kotoba.sprite2d.batch` (new)

A separate, independent layer for **texture-atlas sprite-sheet** rendering — complementary to,
not a replacement for, the vector-shape painter above. These three namespaces are pure CLJC and
stand alone; require whichever you need:

- `kotoba.sprite2d.atlas` — resolves a frame name/index to its pixel rect or normalized UV rect
  within a texture atlas. Supports both explicit sheet atlases (`{:frames {frame-name rect}}`)
  and uniform grid atlases (`{:grid {:cell-w :cell-h :cols :rows}}`, addressable by index,
  `[col row]`, or name via an optional `:names` mapping).
- `kotoba.sprite2d.animation` — frame-based animation clip playback. A clip is a sequence of
  atlas frames plus per-frame `:durations` or a uniform `:fps`, with `:loop`/`:once`/
  `:ping-pong` modes. `advance` is a pure `(clip-state, dt) -> clip-state` step function; callers
  own the reduce/atom.
- `kotoba.sprite2d.batch` — groups sprite instances by `:texture-id` (for draw-call batching)
  and stable-sorts within each group by `:layer`. Output is plain pure data
  (`[{:texture-id :instances [...]}]`) — deliberately GPU-API-agnostic so it can feed a future
  render-IR layer (e.g. the parallel `kotoba-lang/webgl` work) without depending on it.

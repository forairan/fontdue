# Fontdue

[![Documentation](https://travis-ci.org/mooman219/fontdue.svg?branch=master)](https://travis-ci.org/mooman219/fontdue)
[![Documentation](https://docs.rs/fontdue/badge.svg)](https://docs.rs/fontdue)
[![Crates.io](https://img.shields.io/crates/v/fontdue.svg)](https://crates.io/crates/fontdue)
[![License](https://img.shields.io/crates/l/fontdue.svg)](https://github.com/mooman219/fontdue/blob/master/LICENSE)

Fontdue is a simple, `no_std`, pure Rust, truetype font parser and rasterizer. It aims to support all valid (unicode encoded) TrueType fonts correctly, and strives to make interacting with fonts as fast as possible. This includes: layout and rasterization.

A non-goal of this library is to be allocation free and have a fast, "zero cost" initial load. This libary _does_ make allocations and depends on the `alloc` crate. Fonts are fully parsed on creation and relevant information is stored in a more convenient to access format. Unlike other font libraries, the font structures have no lifetime dependencies since it allocates its own space.

Ideally, font loading should be faster in the future, but making the loading process correct and readable was the initial priority.

## TrueType Table Support
- `cmap` Character to glyph mapping (Unicode only)
  - Supports popular formats 0, 4, 6, 10, 12, 13
  - Planned support: formats 2, 8, 14
- `glyf` Glyph outlining
  - Planned support: Compound glyph matched points, compound glyph scaled offset
- `head` General font information
- `hhea` General horizontal layout (Optional)
- `hmtx` Glyph horizontal layout (Optional)
- `vhea` General vertical layout (Optional)
- `vmtx` Glyph vertical layout (Optional)
- `loca` Glyph outline offsets and lengths
- `maxp` Maximum values used for the font

Planned support for:
- `kern` Kerning pair layout

## Example

```rust
// Read the font data.
let font = include_bytes!("../resources/Roboto-Regular.ttf") as &[u8];
// Parse it into the font type.
let mut font = fontdue::Font::from_bytes(font).unwrap();
// Rasterize and get the layout metrics for the letter 'g' at 17px.
let (metrics, bitmap) = font.rasterize('g', 17.0);
```

## Performance

Strives to be the fastest.

### Fontdue

Here are some benchmarks. They generate the layout metrics and bitmap for the letter 'g' are different sizes. This is going straight from the character 'g' to the metrics and bitmap, which is how the majority of people will interact with a font library and is the expected real world performance.

```
Fontdue: Metrics + Rasterize 'g'/20  time:   [1.2332 us 1.2524 us 1.2761 us]
Fontdue: Metrics + Rasterize 'g'/40  time:   [1.7186 us 1.7316 us 1.7454 us]
Fontdue: Metrics + Rasterize 'g'/60  time:   [2.4010 us 2.4125 us 2.4245 us]
Fontdue: Metrics + Rasterize 'g'/80  time:   [3.2217 us 3.2385 us 3.2593 us]
```

### RustType

Other popular font library.

```
RustType: Metrics + Rasterize 'g'/20 time:   [11.519 us 11.540 us 11.565 us]
RustType: Metrics + Rasterize 'g'/40 time:   [18.607 us 18.645 us 18.688 us]
RustType: Metrics + Rasterize 'g'/60 time:   [26.687 us 26.750 us 26.825 us]
RustType: Metrics + Rasterize 'g'/80 time:   [36.481 us 36.591 us 36.711 us]
```

## Attribution

Inspired by how simple the wonderful `rusttype` crate made font parsing look ([link](https://gitlab.redox-os.org/redox-os/rusttype)), and how fast the `font-rs` crate made rasterization look ([link](https://github.com/raphlinus/font-rs)).

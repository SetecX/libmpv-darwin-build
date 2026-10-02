# IptvGuide build of libmpv-darwin-build

This branch (`iptvguide-captions`) builds the macOS libmpv used by
[IptvGuide](https://github.com/SetecX/IptvGuide). It is media-kit's
[libmpv-darwin-build](https://github.com/media-kit/libmpv-darwin-build) at
**v0.6.0** (mpv 0.36.0, FFmpeg 6.0), `video` variant, `default` flavour, with:

| Change | Why |
|---|---|
| `--enable-decoder=ccaption` (`scripts/ffmpeg/meson.build`) | media-kit's builds have no closed-caption decoder, so mpv drops US broadcast (EIA-608 / CEA-708) caption tracks. |
| `patches/ffmpeg-ccaption-default-first-field.patch` | FFmpeg's `data_field=auto` locks onto the field of the first cc_data pair it sees, so after a seek or a mid-stream join it often shows field 2 (CC3/CC4, another language, and XDS data) as text. The default becomes the first field (CC1). mpv cannot pass decoder options to subtitle decoders. |

Build-only changes, needed because v0.6.0's macos-13 / Xcode 14 runners are
gone; they do not change the output:

- `.github/workflows/ci.yaml` builds only
  `libmpv-xcframeworks_<tag>_macos-universal-video-default.tar.gz` on
  `macos-14` and releases it for `v*` tags.
- meson 1.2.1 and cmake 3.27.5, as pinned in `.tool-versions`.
- `cmake = ['cmake']` in `cross-files/macos-*.ini` (newer meson needs it).
- `-Wno-error=...` in the macOS cross files: newer clang turns old warnings
  in pkg-config's bundled glib into errors.

Tags must be letters and digits after `v` (e.g. `v0.6.0cc2`). The recipe
splits file names on `-` and `_`.

Licence: as upstream. FFmpeg is LGPL 3.0-or-later (`--enable-version3`, no
`--enable-gpl` or `--enable-nonfree`). mpv is LGPL 2.1-or-later.

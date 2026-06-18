# Modern Codecs Overview

## Quick recommendations

| Goal                         | Recommended codec | Recommended tool/library               | Container / extension            |
| ---------------------------- | ----------------- | -------------------------------------- | -------------------------------- |
| Compressed video             | **AV1**           | **FFmpeg + `libsvtav1`**               | `.mkv` or `.mp4`                 |
| Compatible compressed video  | **HEVC / H.265**  | FFmpeg + `libx265` or hardware encoder | `.mkv` or `.mp4`                 |
|                              |                   |                                        |                                  |
| Compressed audio             | **Opus**          | FFmpeg + `libopus`                     | usually `.mkv`, `.webm`, `.opus` |
| Lossless audio               | **FLAC**          | FFmpeg `flac` encoder                  | `.flac`, `.mkv`                  |
|                              |                   |                                        |                                  |
| Compressed images            | **AVIF**          | **`libavif` → `avifenc`**              | `.avif`                          |
| Compatible compressed images | **WebP**          | **`libwebp`**, ImageMagick             | `.webp`                          |

---

## Video codecs

| Codec            | Lossy / lossless              |                    Compression quality | Main purpose today                                                                             | Recommended library/tool                          |
| ---------------- | ----------------------------- | -------------------------------------: | ---------------------------------------------------------------------------------------------- | ------------------------------------------------- |
| **AV1**          | Lossy; lossless modes exist   |                              Excellent | Efficient 4K video, streaming, long-term storage, smaller files than H.264/H.265 in many cases | Encode: **FFmpeg + `libsvtav1`**; decode: `dav1d` |
| **HEVC / H.265** | Lossy; lossless modes exist   |                              Very good | 4K/HDR video, TVs, phones, Apple ecosystem, Blu-ray-style compatibility                        | FFmpeg + `libx265` or hardware HEVC encoder       |
|                  |                               |                                        |                                                                                                |                                                   |
| **VVC / H.266**  | Lossy                         |                    Excellent in theory | Future/high-efficiency video, but not practical for most users yet                             | Not recommended for normal use yet                |
| **AV2**          | Lossy                         |                    Future/experimental | Successor to AV1, not ready for normal video libraries yet                                     | Not recommended yet                               |
|                  |                               |                                        |                                                                                                |                                                   |
| **ProRes**       | Lossy, visually near-lossless |                   Poor for small files | Editing/intermediate/master files                                                              | FFmpeg `prores_ks`                                |
| **FFV1**         | Lossless                      | Large but efficient for lossless video | Archival/master preservation                                                                   | FFmpeg `ffv1`, usually in `.mkv`                  |

### Video recommendation

compressed 4K video with good quality

```bash
ffmpeg -i input.mkv \
  -map 0 \
  -c:v libsvtav1 -crf 28 -preset 6 -pix_fmt yuv420p10le \
  -c:a libopus -b:a 160k \
  output.mkv
```

Good starting values for AV1 with SVT-AV1:

| Use case                    |   CRF | Preset |
| --------------------------- | ----: | -----: |
| Higher quality 4K           | 24–26 |    4–6 |
| Balanced 4K                 | 26–30 |    5–7 |
| Smaller/faster test encodes | 30–34 |    7–9 |

Lower `CRF` means better quality and larger files. Lower `preset` means slower encoding and usually better compression.

### AV1 encoder options

| Encoder         | Use it when...                                                  | Pros                                                                   | Cons                                                               |
| --------------- | --------------------------------------------------------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------ |
| **`libsvtav1`** | You want the best normal AV1 encoder for real video compression | Fast, mature, good multi-core scaling, very good quality/size tradeoff | Not always the absolute smallest possible file in every niche case |
| `librav1e`      | You want to experiment with the Rust AV1 encoder                | Interesting, simple, safe Rust implementation                          | Usually not the best practical default compared with SVT-AV1       |
| `libaom-av1`    | You want reference-quality experiments or very fine control     | Very flexible, reference encoder                                       | Often much slower                                                  |

**Recommendation:** use **`libsvtav1`** for normal AV1 video encoding. They are available in FFmpeg when the FFmpeg build was compiled with the matching libraries.

### AV1 workflow tools

| Tool      | What it is                      | Use it when...                                                                                                       | Notes                                                                                                                                                 |
| --------- | ------------------------------- | -------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Av1an** | Encoding framework/orchestrator | You want scene/chunk-based encoding, better CPU use on large machines, resume support, VMAF/target-quality workflows | Not a codec and not part of FFmpeg. It drives standalone encoders such as `SvtAv1EncApp`, `aomenc`, or `rav1e`, plus FFmpeg/VapourSynth/muxing tools. |

Simple Av1an starting point with SVT-AV1:

```bash
av1an -i input.mkv -o output.mkv \
  -e svt-av1 \
  -v "--preset 5 --crf 32 --input-depth 10 --tune 0" \
  -a "-c:a libopus -b:a 160k"
```

Use plain FFmpeg + `libsvtav1` for simple one-off encodes. Use Av1an when the workflow benefits from chunking, scene handling, resume support, or quality probing.

---

## Audio codecs

| Codec         | Lossy / lossless | Compression quality | Main purpose today                                                                       | Recommended library/tool  |
| ------------- | ---------------- | ------------------: | ---------------------------------------------------------------------------------------- | ------------------------- |
| **Opus**      | Lossy            |           Excellent | Best compressed audio, speech, music, video files, low-to-medium bitrate, Surround sound | **FFmpeg + `libopus`**    |
| **AAC**       | Lossy            |           Very good | Best compatibility with MP4, phones, browsers, Apple devices                             | FFmpeg native AAC encoder |
|               |                  |                     |                                                                                          |                           |
| **FLAC**      | Lossless         |        Medium/large | Lossless music/archive audio                                                             | FFmpeg `flac`             |
| **ALAC**      | Lossless         |        Medium/large | Apple lossless audio                                                                     | FFmpeg `alac`             |
|               |                  |                     |                                                                                          |                           |
| **WAV / PCM** | Uncompressed     |                Huge | Editing/production/raw audio                                                             | FFmpeg `pcm_*`            |

### Audio recommendation

For compressed video files:

```text
Stereo: Opus 128–160 kbps
5.1:    Opus 256–384 kbps
```

Example:

```bash
ffmpeg -i input.mkv \
  -c:v copy \
  -c:a libopus -b:a 160k \
  output.mkv
```

**Recommendation:** use **Opus** for `.mkv`/`.webm`; use **AAC** for compatibility.

---

## Image codecs

| Codec / format    | Lossy / lossless                 |           Compression quality | Main purpose today                                                                   | Recommended tool/library              |
| ----------------- | -------------------------------- | ----------------------------: | ------------------------------------------------------------------------------------ | ------------------------------------- |
| **AVIF**          | Lossy and lossless               |                     Excellent | Modern small web images, photos, screenshots, transparency, HDR-capable images       | **`libavif` / `avifenc`**             |
| **JPEG XL / JXL** | Lossy and lossless               |                     Excellent | Modern image format with strong compression and features; browser support is limited | `libjxl` tools                        |
| **PNG**           | Lossless                         | Good for graphics, not photos | Screenshots, UI, logos, transparency, exact pixels                                   | PNG tools, ImageMagick, libvips       |
| **WebP**          | Lossy and lossless               |                     Very good | Broad web support, smaller than JPEG/PNG in many cases                               | `libwebp` tools, ImageMagick, libvips |
| **HEIC / HEIF**   | Usually lossy; lossless possible |                     Very good | Phone photos, especially Apple devices                                               | `libheif`                             |
| **TIFF**          | Often lossless or uncompressed   |                         Large | Scanning, print, professional/archive workflows                                      | ImageMagick, libvips                  |

### Image recommendation on Arch Linux

Install `libavif` & convert to AVIF with:

```bash
avifenc -q 70 -s 6 input.png output.avif
```

For UI screenshots, logos, text, and alpha transparency:

```bash
avifenc -q 80 --qalpha 100 -s 4 --yuv 444 input.png output.avif
```

For lossless AVIF:

```bash
avifenc --lossless -s 4 input.png output.avif
```

### Image tool choice

| Tool                      | Use it when...                            | Pros                                                     | Cons                                      |
| ------------------------- | ----------------------------------------- | -------------------------------------------------------- | ----------------------------------------- |
| **`libavif` / `avifenc`** | You specifically want AVIF                | Best direct AVIF CLI, good control, clean Arch package   | Less convenient for resize/crop pipelines |
| **ImageMagick**           | You want easy one-off conversion/resizing | Very convenient, supports many formats                   | Less AVIF-specific control                |
| **libvips**               | You process many images or large images   | Very fast, low memory, excellent for batch/web pipelines | CLI syntax is less friendly               |
| **libwebp tools**         | You specifically want WebP                | Simple, mature WebP tools                                | Only for WebP                             |
| **libjxl tools**          | You specifically want JPEG XL             | Excellent format technically                             | Compatibility/browser support is weaker   |

Examples:

```bash
# ImageMagick convenience
magick input.png -resize 1600x -quality 70 output.avif
```

```bash
# libvips batch/pipeline style
vips copy input.png 'output.avif[Q=70,effort=6]'
```

---

## Personal default choices

For a modern media workflow:

```text
Video:  AV1 via FFmpeg + libsvtav1
Audio:  Opus via FFmpeg + libopus
Images: AVIF via libavif / avifenc
```

For compatibility-focused files:

```text
Video:  HEVC/H.265 or H.264
Audio:  AAC or E-AC-3
Images: WebP or JPEG/PNG
```

For archival/lossless preservation:

```text
Video:  Original stream copy, FFV1, or high-quality HEVC/AV1 if re-encoding is acceptable
Audio:  FLAC or original lossless stream
Images: PNG, TIFF, lossless AVIF, or JPEG XL
```

---

## Software packages

```bash
# Video/audio workhorse
$ ffmpeg svt-av1 dav1d opus

# Optional AV1 encoding workflow
$ av1an mkvtoolnix-cli vapoursynth-plugin-bestsource ffms2

# AVIF images
$ libavif

# Convenient image conversion
$ imagemagick libheif

# Fast image pipelines
$ libvips libheif

# WebP tools
$ libwebp

# JPEG XL tools
$ libjxl
```

---

## Sources / documentation

- FFmpeg AV1 encoding guide: <https://trac.ffmpeg.org/wiki/Encode/AV1>
- FFmpeg codec documentation: <https://ffmpeg.org/ffmpeg-codecs.html>
- dav1d AV1 decoder: <https://code.videolan.org/videolan/dav1d>
- Av1an documentation: <https://rust-av.github.io/Av1an/>
- Av1an source: <https://github.com/rust-av/Av1an>
- SVT-AV1 FFmpeg usage: <https://gitlab.com/AOMediaCodec/SVT-AV1/-/blob/master/Docs/Ffmpeg.md>
- Arch manual `avifenc`: <https://man.archlinux.org/man/avifenc.1.en>
- Opus RFC 6716: <https://datatracker.ietf.org/doc/html/rfc6716>
- Opus project: <https://opus-codec.org/>
- Fraunhofer AAC-LC overview: <https://www.iis.fraunhofer.de/en/ff/amm/broadcast-streaming/aaclc.html>
- libvips HEIF/AVIF save options: <https://www.libvips.org/API/current/method.Image.heifsave.html>
- ImageMagick supported formats: <https://imagemagick.org/formats/>

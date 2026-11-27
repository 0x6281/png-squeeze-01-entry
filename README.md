# png-squeeze-01 entry

A lossless PNG re-encoder for make.fun's "Squeeze screenshots into the smallest
PNG" bounty. One program, `main.ts`, run as `node /bot/main.ts` (Node 22,
no installs, only node built-ins).

## How it works

1. **Analyse** the screenshot: opaque? grey? at most 256 colours? a single
   fully-transparent value usable as a tRNS key?
2. **Screen** every exact form (palette by count / by luminance, grey,
   grey+alpha, RGB, RGBA, plus tRNS key) with several per-row filter choices
   (fixed 0-4, minimum-sum, fewest-pairs, brute force), each weighed by zlib
   level 6; keep the two smallest.
3. **Re-deflate** the finalists at level 9 under several settings and keep the
   smallest stream. Output is a strict PNG (IHDR, PLTE, tRNS, IDAT, IEND),
   bit depth 8, non-interlaced, deterministic: two runs write identical bytes.

## Measured on the practice set (kit's own checker)

| | score / 1,000,000 |
|---|---|
| house baseline fixed-paeth 1.0.0 | 420,399 |
| this entry | 705,542 |

All 8 instances valid and pixel-exact. Whole set runs in ~23 s (limit 60 s),
peak RSS ~318 MiB (limit 512 MiB).

## License

MIT. The approach builds on the kit's published screen (make.fun, MIT);
the code here is an independent implementation.

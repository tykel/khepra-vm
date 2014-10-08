Khepra VM overview
==================

CPU [[link](CPU.md)]
---

- 16-bit, `3.93216 MHz`
- `65536` cycles per frame @ 60 Hz
- Orthogonal ISA, MMIO for cleanliness
- Registers: `a, b, c, d, e, p, s, f`
- Programmable interrupt vector, 8x entries
- Counter available
  - Available freqs.: 60, 120, 240, 480, 960 Hz

Memory [[link](Memory.md)]
------

- `64 KB` address space
- `32 KB` ROM + `8 KB` tile ROM + `2 KB` audio ROM + `16 KB` RAM visible at any point in time
  - Bank swapping possible for ROM/RAM by writing to MMIO registers
  - Total: `4+ MB` ROM + `2 MB` tile ROM + `512 KB` audio ROM + `2+ MB` RAM theoretical maximum

Video [[link](Video.md)]
-----

- `256x224` screen resolution
- `60` Hz refresh rate
- Tile-based graphics - `8x8` pixels
  - `256` hardware colors
  - `16x` `16`-color palettes, definable at run-time
  - Separate layer1/layer2/sprite palettes: up to `48` colors simultaneously
- Hardware sprites `64x`
  - Sprite clustering supported in hardware

Audio [[link](Audio.md)]
-----

- 4 audio channels
- 4 different wave types: square/pulse, triangle, noise, (D)PCM

Input
-----

- 2x controller ports
  - Controller: `D-Up, D-Down, D-Left, D-Right, Start, Select, A, B, L, R`
  - The shift registers containing controller state are updated at each `VBLANK`
  signal
- 1x serial port for various use
  - Reserved for future expansion (for now)

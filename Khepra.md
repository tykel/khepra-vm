Khepra VM overview
==================

[CPU](CPU.md)
---

- 16-bit, `3.93216 MHz`
- `65536` cycles per frame @ 60 Hz
- Orthogonal ISA, MMIO for cleanliness
- Registers: `a, b, c, d, e, p, s, f`
  - `a,b,c,d,e` are general-purpose
  - `p` is instruction pointer / program counter
  - `s` is stack pointer
  - `f` is flags register
- Programmable interrupt vector, 8x entries
- 4x counters available
  - Available freqs.: 30, 60, 120, 240, 480, 960 Hz

Memory
------

- `64 KB` address space

  | Range | Usage|
  |-------|------|
  | `fff8 - ffff` | interrupt vector |
  | `fff4 - fff7` | serial port shift reg. |
  | `fff2 - fff3` | pad 2 shift reg. |
  | `fff0 - fff1` | pad 1 shift reg. |
  | `ffe6 - ffef` | - |
  | `ffe2 - ffe5` | hi-res counter reg. |
  | `ffe1`        | RAM bank select |
  | `ffe0`        | RAM bank select |
  | `ff00 - ffdf` | - |
  | `fe00 - feff` | cartridge perm. storage |
  | `f800 - fdff` | - |
  | `f000 - f7ff` | 2K dpcm swap. ROM bank |
  | `ecf0 - efff` | - |
  | `ece0 - ecef` | audio dpcm rate map |
  | `ecd0 - ecdf` | audio release map |
  | `ecc0 - eccf` | audio sustain map |
  | `ecb0 - ecbf` | audio decay map |
  | `eca0 - ecaf` | audio attack map |
  | `ec90 - ec9f` | audio length map |
  | `ec10 - ec8f` | audio tone map |
  | `ec00 - ec0f` | hw. audio reg. |
  | `eb51 - ebff` | - |
  | `eb50`        | tile ROM bank select |
  | `eb46 - eb49` | layer 2 scroll reg. |
  | `eb42 - eb45` | layer 1 scroll reg. |
  | `eb41`        | sprite palette index |
  | `eb40`        | layers 1-2 palette indexes |
  | `eac0 - eb3f` | hw. sprite coord. |
  | `ea00 - eabf` | hw. sprite reg. |
  | `e900 - e9ff` | palettes 1-16 |
  | `e480 - e8ff` | layer 2 tilemap |
  | `e000 - e47f` | layer 1 tilemap |
  | `c000 - dfff` | 8K tile swap. ROM bank |
  | `a000 - bfff` | 8K swap. RAM bank |
  | `8000 - 9fff` | 8K  fixed RAM bank |
  | `4000 - 7fff` | 16K swap. ROM bank |
  | `0000 - 3fff` | 16K fixed ROM bank |

- So: `32 KB` ROM + `8 KB` tile ROM + `2 KB` audio ROM + `16 KB` RAM visible at any point in time
  - Bank swapping possible for ROM/RAM by writing to MMIO registers
  - Total: `4+ MB` ROM + `2 MB` tile ROM + `512 KB` audio ROM + `4+ MB` RAM theoretical maximum

Video
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
- 1x serial port for various use


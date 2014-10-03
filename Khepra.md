Khepra VM overview
==================

CPU
---

- 16-bit, 3.999996 MHz
- 66666 cycles per frame @ 60 Hz
- Orthogonal ISA, MMIO for cleanliness
- Registers: a, b, c, d, e, f, pc, sp
  - f doubles as frame pointer
- Programmable interrupt vector

Memory
------

- 64 KB address space
  
  | fff8 - ffff : interrupt vector
  | fff4 - fff7 : serial port shift reg.
  | fff2 - fff3 : ctlr 2 shift reg.
  | fff0 - fff1 : ctlr 1 shift reg.
  | ffe1        : RAM bank select
  | ffe0        : ROM bank select
  | e454 - e455 : dpcm sample reg.
  | e450 - e453 : hardware audio reg.
  | e441 - e442 : 
  | e440        : h-scroll reg.
  | e0c0 - e43f : screen tilemap
  | e000 - e0bf : hardware sprite reg.
  | a000 - dfff : 16K swap. RAM bank 
  | 8000 - 9fff : 8K  fixed RAM bank (SP = 9ffe, grows back)
  | 4000 - 7fff : 16K swap. ROM bank
  | 0000 - 3fff : 16K fixed ROM bank

- So: 32 KB ROM + 24 KB RAM visible at any point in time
- Mappers supported on cartridge, communicate via MMIO for bank swapping

Video
-----

- 256x224 screen resolution
- 60 Hz refresh rate
- Tile-based graphics - 8x8
  - 64 hardware colors
  - 4x 16-color palettes
- Hardware sprites 64x

Audio
-----

- 4 audio channels
- 4 different wave types: square/pulse, triangle, noise, (D)PCM

Input
-----

- 2x controller ports
  - Controller: Du Dd Dl Dr St Sl A B L R
- 1x serial port for various use


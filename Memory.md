Memory
======

The CPU addresses `64 KB` of memory space.
Khepra maps it in the following way (descending addresses):

###### Miscelleneous
| Range | Usage |
|-------|-------|
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

###### Audio
| Range | Usage |
|-------|-------|
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

###### Video
| Range | Usage |
|-------|-------|
| `eb91 - ebff` | - |
| `eb90`        | tile ROM bank select |
| `eb86 - eb89` | layer 2 scroll reg. |
| `eb82 - eb85` | layer 1 scroll reg. |
| `eb81`        | sprite palette index |
| `eb80`        | layers 1-2 palette indexes |
| `eb00 - eb7f` | hw. sprite coord. |
| `ea00 - eaff` | hw. sprite reg. |
| `e900 - e9ff` | palettes 1-16 |
| `e480 - e8ff` | layer 2 tilemap |
| `e000 - e47f` | layer 1 tilemap |
| `c000 - dfff` | 8K tile swap. ROM bank |

###### (Program) ROM / RAM
| Range | Usage |
|-------|-------|
| `a000 - bfff` | 8K swap. RAM bank |
| `8000 - 9fff` | 8K  fixed RAM bank |
| `4000 - 7fff` | 16K swap. ROM bank |
| `0000 - 3fff` | 16K fixed ROM bank |

#### Memory banks
RAM, program ROM, tile ROM, and audio ROM are all bank-switched to allow for much
more content.

Writes to bank-select registers are effective from the following cycle.

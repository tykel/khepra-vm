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
| `ffe4 - ffef` | - |
| `ffe2 - ffe3` | hi-res counter reg. |
| `ffe1`        | RAM bank select |
| `ffe0`        | ROM bank select |
| `ff00 - ffdf` | - |
| `fe00 - feff` | cartridge perm. storage |

###### Audio
| Range | Usage |
|-------|-------|
| `f800 - fdff` | - |
| `f000 - f7ff` | 2K dpcm swap. ROM bank |
| `ecf1 - efff` | - |
| `ecf0`        | audio ROM bank select |
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

#### Gamepad shift registers
The game controllers are updated by the system when the `VBLANK` signal is sent.
The state is serially transmitted from the controller to a 16-bit serial-to-parallel shift
register, present at `$fff0` for controller 1, and `$fff2` for controller 2.

They encode the state in the following way:

| `_ _ _ _  _ _ R L` | | `B A s S  > < V ^` |
|-------------------|---|-------------------|

- `^`, `V`, `<`, `>` are the directional pad buttons
- `S` is the `START` button
- `s` is the `SELECT` button
- `A` is the `A` button
- `B` is the `B` button
- `L` is the left trigger `L` button
- `R` is the right trigger `R` button

*Note: it is physically impossible for Up and Down, or Left and Right to be
pressed simultaneously on the controller. Emulators should preserve this behaviour.*

#### Timer
There is a timer available, which counts down a desired cycle number. It may also be used to synchronize to the `H-Blank` signal at every scanline.

The timer register at `$ffe2-3` has the following format:

| `V V V V  V V H E`  | | `V V V V  V V V V` |
|---------------------|---|---------------------|

- `E`: Enable timer.
- `H`: Fire an interrupt at H-Blank. If this bit is set, the remaining bits are ignored.
- `V`: Fire an interrupt after `V * 4` cycles (little-endian!), if `H == 0`.

Note: If `H` was set while the timer was in the middle of a cycle countdown, and then cleared again, the countdown restarts from `V * 4`; i.e., the state is cleared.

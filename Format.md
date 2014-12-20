ROM File Format
===============

The standard file format for storing and exchanging program ROM dumps is described
here.

The proposed file extension is `.kpr`, for KhePra Rom.

#### File header

| Offset | Length | Value | Description |
|--------|--------|-------|-------------|
| `$0000`|`4`     |`"KHPR"`| Magic number |
| `$0004`|`4`     | `$xxxx` (S) | Total file size (incl. this header) |
| `$0004`|`4`     | `$xxxx` (C) | CRC32 checksum (excl. this header) |
| `$0008`|`1`     | `$xx` (P) | Number of Program ROM banks |
| `$0009`|`1`     | `$xx` (R) | Number of RAM banks |
| `$000a`|`1`     | `$xx` (T) *(T >= 1)* | Number of Tile ROM banks |
| `$000b`|`1`     | `$xx` (A) | Number of Audio ROM banks |
| `$000c`|`4`     | `0000`  | Reserved |
| `$0010`|`16`    | `"..."` (N) | ROM Name |
| `$0020`|`32`    | `"..."` (D) | ROM Description |

#### Data

| Offset | Length | Description |
|--------|--------|-------------|
| `$0040`| `16,384`| ROM bank @ `$0000`|
| `$4040`| `16,384 * P`| ROM banks @ `$4000`|
| `$4040 + $4000*P`| `8,192 * T`| Tile ROM banks @ `$c000`|
| `$4040 + $4000*P + $2000*T`| `2,048 * A`| Audio DPCM ROM banks @ `$f000`|
| `$4040 + $4000*P + $2000*T + $800*A`| `256`| Permanent storage @ `$fe00`|

*Note: the "Permanent storage" section is writable by the emulator*.

Total size: `$4140 + $4000*P + $2000*T + $800*A`

E.g. for a ROM with 1 extra RAM bank, 2 extra ROM banks, 5 tile ROM banks and 2
audio ROM banks, the size will be:
* `$6140 + $4000*2 + $2000*1 + $2000*5 + $800*2` = `$1b140` = `110,916` bytes.

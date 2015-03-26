ROM File Format
===============

The standard file format for storing and exchanging program ROM dumps is described
here.

The proposed file extension is `.kpr`, for `K`he`P`ra `R`om, but others may be used.

#### File header

| Offset | Length | Value | Description |
|--------|--------|-------|-------------|
| `$0000`|`4`     |`"KHPR"`| Magic number |
| `$0004`|`4`     | `$xxxx` (S) | Total file size (incl. this header) |
| `$0008`|`4`     | `$xxxx` (C) | CRC32 checksum (excl. this header) |
| `$000c`|`1`     | `$xx` (P) | Number of Program ROM banks |
| `$000d`|`1`     | `$xx` (R) | Number of RAM banks |
| `$000e`|`1`     | `$xx` (T) *(T >= 1)* | Number of Tile ROM banks |
| `$000f`|`1`     | `$xx` (A) | Number of Audio ROM banks |
| `$0010`|`4`     | `0000`  | Reserved |
| `$0014`|`16`    | `"..."` (N) | ROM Name |
| `$0024`|`32`    | `"..."` (D) | ROM Description |

Total header size: `$44` = `68` bytes.

#### Data

Each memory bank is preceded by a `4`-byte header:

| Offset | Length | Value | Description |
|--------|--------|-------|-------------|
|`$0000` |`1`     |`$xx` (T)| Bank type   |
|`$0001` |`1`     |`$xx` (N)| Bank number (swap. banks only)|
|`$0002` |`2`     |`$xxxx` (S)| Bank size (must match spec.)|

The bank data of size `S` follow thereafter.
If `S` does not match the size of the bank as defined in the memory map, the loading shall fail.

`T` may have the following values:
- `0`: Fixed ROM bank @ `$0000`
- `1`: Swap. ROM bank @ `$4000`
- `2`: Fixed RAM bank @ `$8000`
- `3`: Swap. RAM bank @ `$a000`
- `4`: Swap. tile bank @ `$c000`
- `5`: Swap. audio bank @ `$f000`

NOTE: Banks may be in any order in the file.

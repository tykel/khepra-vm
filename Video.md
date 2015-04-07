Video
=====

Khepra outputs a `256x224` pixel picture, at 60 Hz.

Khepra has hardware support for 64 sprites, and 48 simultaneous colors on-screen
(from a palette of 256). There are also 2 tilemap layers.

####Sprites
Each sprite is `8x8` pixels. Each pixel in the sprite is stored in `4` bits; so 
*one* sprite takes up `32 B`.

All sprites share a common 16 color palette, the index of which is stored in
`$eb81`, bits `3-0`. The palettes are stored between `$e900` and `$e9ff`, and
they index from the global, built-in 256-color palette.

Each sprite is controlled through a `4 B` register, which are found between
`$ea00` and `$eabf`. They have the following format:

|`E Z Z Z  M D m d` | | `_ _ G G  G G G G` | | `X X X X  Y Y Y Y` | | `T T T T  T T T T` |
|------------------|---|------------------|---|------------------|---|-------------------|

- `E` controls whether the sprite is Enabled (if `E=0`, it is not drawn).
- `Z` is the depth or Z-index. Index `0` is drawn at the top, `7` at the bottom.
  - If two or more sprites share the same `Z`, sprites with higher register
  addresses will be drawn on top - e.g. `$ea04` would be drawn above `$ea00`.
- `M` and `m` control horizontal and vertical mirroring, respectively.
- `D` and `d` control horizontal and vertical doubling, respectively.
  - Doubling means each pixel is drawn twice in a given dimension, effectively
  doubling that dimension.
- `G` is the tile Group index, which is used to lookup the sprite's position in
the sprite group coordinate vector at `$eac0`.
- `X` and `Y` are the offsets, in multiples of 8 pixels, to draw this sprite
from the group coordinate on the x-axis and y-axis respectively.
  - A sprite with offset `(-2, 0)` will be drawn 16 pixels to the left, and at the
  same y-coordinate, as its group coordinate.
- `T` is the tilemap index, indicating which tile to use for the sprite.

#### Groups
*Groups* are used for sprite coordinates. Each coordinate entry is associated with
a group index, and multiple sprites may share a group index. This allows groups
of sprites forming a larger sprite (e.g. 4 sprites forming a `16x16` "meta-sprite")
to move together.

The sprite group coordinate entries are `2 B` each, available from `$eb00` to `$eb7f`.
They have the following format:

|`X X X X X X X X`| |`Y Y Y Y Y Y Y Y`|
|----------------|---|----------------|

- `X` is the x-coordinate, in pixels. `[0-255]`
- `Y` is the y-coordinate, in pixels. `[0-223]` `[224-255 treated as 223]`

#### Palettes
As mentioned previously, there are three palettes: one for each tilemap layer,
and one for sprites. They are one of the palettes between `$e900` and `$e9ff`.

The tilemap palette indexes are at `$eb40`:

|`H H H H L L L L`|
|-----------------|

- `L` is layer 1's palette index.
- `H` is layer 2's palette index.

The sprites' palette index is at `$eb81`:

|`_ _ _ _ P P P P`|
|-----------------|

- `P` is the sprites' palette index.

The palette used is at `[$e900 + 16*i]`, where `i` is the index.

Palette entry `0` is **always** transparent for sprites and layer 1.
For layer 2, that color is displayed normally.

At startup, all palettes are zeroed, so all colors begin as black (`#000000`).

###### Global (fixed) palette
Each palette indexes into the 256-color global palette, which is internal and not
modifiable.

This palette is the same one as used in Deluxe Paint 4 for the Amiga - see
[palette.txt](palette.txt) for the list.

#### Scrolling
There are 2 scrolling mechanisms available:
- *coarse* scrolling, which allows tilewise scrolling
- *fine* scrolling, which allows intra-tile scrolling

###### Coarse scrolling
For scroll values `H [0-35]` , `V [0-31]`:
- Renders columns `H` to `(H + 32) % 36`
- Renders rows `V` to `(V + 28) % 32`
Notice that the tilemap may wrap around if necessary. Scroll values wrap if outside range. 

Coarse scroll registers are at `$eb82` (X) and `$eb84` (Y) for layer 1; and `$eb86`
(X) and `$eb88` (Y) for layer 2.

###### Fine scrolling
For scroll values `h [0-7]`, `v [0-7]`:
- Renders columns with `h` pixels offset. Say columns `L` to `R` are visible:
  - e.g. leftmost `h` pixels from column `L` missing, leftmost `h` pixels from column `(R + 1) % 36` visible
- Similarly for rows with `v`.

Fine scroll registers are at `$eb83` (X) and `$eb85` (Y) for layer 1; and `$eb87`
(X) and `$eb89` (Y) for layer 2.

#### Tile ROM
Tilemaps for the layers and the sprites index tiles from the tile ROM, in memory
between `$c000` and `$dfff`.
It is bank-switchable, using the ROM bank select register at `$eb90`. There can
be up to 256 banks as a result, for a total of up to `8 KB * 256 = 2 MB` of tile data
(65,536 tiles).

#### Timing/Interrupts
The video chip spends most of the frame drawing to the screen. Updating the screen
contents can only be done safely during the **vertical blanking** period, or `VBLANK`.

This period occurs at the end of each frame, when the electron beam is disabled
and moves back to the upper left of the screen for the next frame. Khepra renders
to a simplified NTSC signal, with 60 frames per second. Each frame is comprised
of 224 displayed lines, and each line has 256 distinct pixels.

The `VBLANK` period lasts exactly `6000` cycles, or `~1.525879` milliseconds. An
interrupt is raised at the beginning of this period, and jumps to the appropriate
handler specified in `$fff8`.

Additionally, an `HBLANK` period occurs at the end of each scanline. A scanline lasts `17527.5` cycles, or `4.45747` milliseconds, of which the `HBLANK` is the last `119.5` cycles. Thus, a pixel is drawn every `68` cycles.

Any memory accesses to video chip memory done outside of `VBLANK` are simply ignored.

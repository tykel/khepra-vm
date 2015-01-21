Audio
=====

Khepra has a 4-channel audio processing unit.

Pulse (square) wave generator
-----------------------------
Register at `ec00 - ec03`.

|`_ _ F F  F F F F`| |`E V V V  L L L L`| |`R R R R  A A A A`| |`S S S S  D D D D`|
|-----------------|---|----------------|---|----------------|---|-----------------|

- `L` is the length of the sound.
 * `ms = L/240` (16-value lookup, 0-255)
- `V` is the volume of the sound. (8 levels)
- `E` toggles whether to use the envelope parameters, or play at constant volume.
- `F` is the frequency or "note" of the sound.
 * Hz = `3932160/(32*(F+1))` (64-value 16-bit lookup, 0 - 4,096)
- `A` is attack, which specifies how long it takes, in ms, to reach full volume. (16-value lookup)
- `R` is release, which specifies how long it takes, in ms, to fall back to silence. (16-value lookup)

Triangle wave generator
-----------------------
Register at `ec04 - ec07`.
Same register layout as pulse.

Noise generator
---------------
Register at `ec08 - ec0b`.
Same register layout as pulse.

Delta-modulated channel
---------------
Register at `ec0c - ec0f`.

|`S S S S S S S S`| |`F F F F I S S S`| |`B B B B B B B B`| |`O O O O O O O O`|
|----------------|---|---------------|---|---------------|---|----------------|

- `S` is the size of the sample, in bytes (0 - 2048)
- `F` is the playback frequency (16-value lookup)
- `I` enables an IRQ at playback end
- `B` is the bank index, used for bank switching on the sample ROM bank
- `O` is the offset used to calculate sample start address.
  * `A: 1111 0OOO OOOO O000` in binary or `Addr = $f000 + 8*Offs` in hex

DMC samples are 1-bit delta-modulated samples at rate looked up from `ec0d` bits 3-6.

Sample output has 16 levels.

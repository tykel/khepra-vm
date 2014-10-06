Audio
=====

Khepra has a 4-channel audio processing unit.

Pulse (square) wave generator
-----------------------------
Register at `ec00 - ec03`.

```        
ec00:   7      0
        xxFFFFFF           F=frequency
        
ec01:   7      0
        EVVVLLLL           L=length, V=volume, E=use envelope
        
ec02:   7      0
        RRRRAAAA           A=attack, R=release

ec03:   7      0
        SSSSDDDD           D=decay, S=sustain
        
```
- Length is the duration of the sound.
 * ms = Length/240 (16-value lookup, 0-255)
- Volume is the loudness of the sound. (8 levels)
- E toggles whether to use the envelope parameters, or play at constant volume.
- Frequency is the "note" of the sound.
 * Hz = 3932160/(32*(F+1)) (64-value 16-bit lookup, 0 - 4,096)
- Attack specifies how long it takes, in ms, to reach full volume. (16-value lookup)
- Release specifies how long it takes, in ms, to fall back to silence. (16-value lookup)

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
```
ec0c:   7      0
        SSSSSSSS           S=size.lo
        
ec0d:   7      0
        FFFFISSS           S=size.hi, I=interrupt, F=freq
        
ec0e:   7      0
        BBBBBBBB           B=bank index           
        
ec0f:   7      0
        OOOOOOOO           O=offset
        
```
- Size is the size in bytes of the sample in bytes (0 - 2048)
- Frequency is the playback frequency (16-value lookup)
- Interrupt enables an IRQ at playback end
- Bank index is used for bank switching on the sample ROM bank
- Offset is used to calculate sample start address.
  - `A: 1111 0OOO OOOO O000` in binary or `Addr = $f000 + 8*Offs` in hex

DMC samples are 1-bit delta-modulated samples at rate looked up from `ec0d` bits 3-6.

Sample output has 16 levels.

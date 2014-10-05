Audio
=====

Khepra has a 4-channel audio processing unit.

Pulse (square) wave generator
-----------------------------
Register at `ec00 - ec03`.

```
ec00:   7      0
        ELLLLLLL           L=length, E=use envelope

ec01:   7      0
        RRRRAAAA           A=attack, R=release
        
ec02:   7      0
        FFFFVVVD           D=decay, V=volume, F=freq.lo
        
ec03:   7      0
        FFFFFFFF           F=freq.hi
        
```
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
        RFFxxxxx           R=repeat, F=freq

ec0d:   7      0
        SSSSSSSS           S=size
        
ec0e:   7      0
        LLLLLLLL           L=length
        
ec0f:   7      0
        xxxxxxxx           
        
```

Additional 16-bit pointer to sample at `ec10`.

DMC samples are 1-bit delta-modulated samples at rate looked up from `ec0c`.

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
-----------------------------
Register at `ec04 - ec07`.

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

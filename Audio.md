Audio
=====

Khepra has a 4-channel audio processing unit.

Pulse (square) wave generator
-----------------------------

Register at `ec00 - ec04`.


```
ec00:   7      0
        AAAAEVVV           V=volume, E=use envelope, A=attack

ec01:   7      0
        LLLDRRRR           R=release, D=decay, L=length.lo
        
ec02:   7      0
        FFFFLLLL           L=length.hi, F=freq. lo
        
ec03:   7      0
        FFFFFFFF           F=freq. hi
```

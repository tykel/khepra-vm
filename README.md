khepra
======

A retro VM specification and implementation.
Sister project [qpra](https://github.com/tykel/qpra) is the first khepra emulator.

What is Khepra?
---
It is a VM designed for making 2D games.
In this respect it is similar to [Chip8](http://en.wikipedia.org/wiki/CHIP-8) and [Chip16](https://github.com/chip16/chip16), among others.

However, Khepra is different because it chooses to sacrifice some simplicity, in order to more closely resemble a late 1980s 8/16-bit video game console. Some features such as bank switched memory add some complexity to the system and to programming, but allow for much larger games.

Khepra also features a more competent audio system than either Chips, with 4-channel sound, including a DPCM playback channel.

**TLDR**; You can think of Khepra as a simplified, beefed up NES.

What are the specs?
---
Please see the [Specifications](Khepra.md) page for an overview of the emulated specifications.

It has similarities with the NES, but has a 16-bit processor.

Are there any games yet? Can I write programs for Khepra?
---
Khepra is in development, so there is no software yet.

You are welcome to write programs targetting this system, and share them!

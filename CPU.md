CPU
===

Khepra has a fully 16-bit processor, with 8 registers, with a 64 KB address space.

Registers
---
There are 8 registers of 16-bit word size:

- `a, b, c, d, e` are general purpose registers
- ` p` is the Program counter register
- `s` is the Stack pointer register
- `f` is the Flags status register.

All registers may be used in instructions.

Instruction structure
---
Instructions are either 16-bit, 24-bit or 32-bit (2, 3 or 4 bytes) depending on their addressing mode.

Byte 0:

| `C C C C  C C` | `M M` |
|---|---|
| Opcode | Addr. mode. hi |

Byte 1:

| `M M` | `X X  X` | `Y Y Y` |
|---|---|---|
| Addr.mode lo | Reg. X | Reg. Y |

Byte 2:

| `D D D D  D D D D` |
|---|
| Data lo |

Byte 3:

| `D D D D  D D D D` |
|---|
| Data hi |

Addressing modes
---

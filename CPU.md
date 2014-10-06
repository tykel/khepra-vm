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
Instructions are either 16-bit, 24-bit or 32-bit (2, 3 or 4 bytes) depending on their addressing mode:

| `CCCC CC`| `MM` | | `MM` | `XX X` | `YYY` |
|---|---|---|---|---|---|
| Opcode | Addr. mode. hi | | Addr.mode lo | Reg. X | Reg. Y |

| `DDDD DDDD` | | ` DDDD DDDD` |
|---|---|---|
| Data lo | | Data hi |
Addressing modes
---

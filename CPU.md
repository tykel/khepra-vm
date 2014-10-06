CPU
===

Khepra has a fully 16-bit processor, with 8 registers, with a 64 KB address space.

Words are interpreted in little-endian ordering.

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

Their are 16 addressing modes, *plus* an implicit mode.

| Mode | Name | Short | Format | Instr. Length |
|------|------|-------|--------|---------------|
| - | Implicit | `U` | `i` | 2 B |
| 0 | Direct reg. | `DR` | `i r0` | 2 B |
| 1 | Indirect reg. | `IR` | `i [r0]` | 2 B |
| 2 | Reg./Direct reg. | `R_DR` | `i r0, r1` | 2 B |
| 3 | Reg./Indirect reg. | `R_IR` | `i r0, [r1]` | 2 B |
| 4 | Reg./Direct byte | `R_DB` | `i r0, $xx` | 3 B |
| 5 | Reg./Indirect byte [byte] | `R_IB1` | `i r0, b[$xx]` | 3 B |
| 6 | Reg./Indirect byte [word] | `R_IB2` | `i r0, [$xx]` | 3 B |
| 7 | Reg./Direct word | `R_DW` | `i r0, $xxxx` | 4 B |
| 8 | Reg./Indirect word [byte] | `R_IW1` | `i r0, b[$xxxx]` | 4 B |
| 9 | Reg./Indirect word [word] | `R_IW2` | `i r0, [$xxxx]` | 4 B |
| a | Direct byte | `DB` | `i $xx` | 3 B |
| b | Indirect byte [byte] | `IB1` | `i b[$ff]` | 3 B |
| c | Indirect byte [word] | `IB2` | `i [$ff]` | 3 B |
| d | Direct word | `DW` | `i $xxxx` | 4 B |
| e | Indirect word [byte] | `IW1` | `i b[$xxxx]` | 4 B |
| f | Indirect word [word] | `IW2` | `i [$xxxx]` | 4 B |

*Note: Indirect byte accesses (modes `5,6,b,c`) are PC relative. They allow accesses from `[PC-128]` to `[PC+127]`.*

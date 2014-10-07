CPU
===

Khepra has a fully 16-bit processor, with 8 registers, and a 64 KB address space.

Words are interpreted in little-endian ordering.

The clock frequency is 3,932,160 cycles per second. [60*65536]

Registers
---
There are 8 registers of 16-bit word size:

- `a, b, c, d, e` are general purpose registers
- ` p` is the Program counter register
- `s` is the Stack pointer register
- `f` is the Flags status register.

All registers may be used in instructions.

Flags register structure:

| `x` | `x` | `x` | `I` | `N` | `O` | `C` | `Z` |
|-----|-----|-----|-----|-----|-----|-----|-----|
||||Interrupt|Negative|Overflow|Carry|Zero|

- `I` specifies whether to handle interrupts
- `N` indicates a result which is negative
- `O` indicates a result which cannot be represented as a *signed* 16-bit value 
- `C` indicates a result which cannot be represented as an *unsigned* 16-bit value
- `Z` indicates a result of 0.

Instruction structure
---
Instructions are either 8-bit, 16-bit, 24-bit or 32-bit (1, 2, 3 or 4 bytes) depending on their addressing mode.

Byte 0:

| `W` | `C C C  C C` | `M M` |
|---|---|---|
| Word? | Opcode | Addr. mode. hi |

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
| - | Void | `V` | `i` | 1 B |
| 0 | Direct reg. | `DR` | `i r0` | 2 B |
| 1 | Indirect reg. | `IR` | `i [r0]` | 2 B |
| 2 | Di.reg./Di.reg. | `DR_DR` | `i r0, r1` | 2 B |
| 3 | Di.reg./Indi.reg. | `DR_IR` | `i r0, [r1]` | 2 B |
| 4 | Indi.reg./Indi.reg. | `IR_DR` | `i [r0], r1` | 2 B |
| 5 | Di.reg./Di.byte | `DR_DB` | `i r0, $xx` | 3 B |
| 6 | Di.reg./Indi.byte offs. | `DR_IB` | `i r0, [$xx]` | 3 B |
| 7 | Di.reg./Di.word | `DR_DW` | `i r0, $xxxx` | 4 B |
| 8 | Di.reg./Indi.word | `DR_IW` | `i r0, [$xxxx]` | 4 B |
| 9 | Di.byte | `DB` | `i $xx` | 3 B |
| a | Indi.byte offs. | `IB` | `i [$xx]` | 3 B |
| b | Di.word | `DW` | `i $xxxx` | 4 B |
| c | Indi.word | `IW` | `i [$xxxx]` | 4 B |
| d | Indi.byte offs./Di.reg | `IB_DR` | `i [$xx], r0` | 3 B |
| e | Indi.word/Di.reg | `IW_DR` | `i [$xxxx], r0` | 4 B |
| f | - | - | - | - |

*Note: Indirect byte accesses (modes `7,b,e`) are PC relative. They allow accesses from `[PC-128]` to `[PC+127]`.*

- The `W` ('Word?') flag governs whether to act on 8-bit or 16-bit (word) operands.
  - On registers, only the lower 8 bits (7-0) will be used. This includes mode 2.
  - For memory accesses, only one byte (8 bits) will be loaded from, or stored to, the address in memory.
  - Assemblers should look for a `.B` suffix to the opcode to signify that `W=0`. An optional `.W` suffix might be used for clarity to signify `W=1`, although it is implied.

- The instructions follow Intel convention, that is: `OP TO, FROM`

- We notice that instructions using the implicit Void mode are only one byte long, even though the addressing mode field runs into byte 2 of the instruction. This is because the first four instructions use implicit mode *only*, so if bits 2-3 are `0`, we stop fetching bytes for the instruction.

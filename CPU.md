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

Bits 15-8: `x`

Bits 7-0:

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

| Mode | Name | Short | Format | Instr. Length | Clocks |
|------|------|-------|--------|---------------|--------|
| - | Void | `V` | `i` | 1 B | 2 |
| 0 | Direct reg. | `DR` | `i r0` | 2 B | 2 |
| 1 | Indirect reg. | `IR` | `i [r0]` | 2 B | 4 |
| 2 | Di.byte | `DB` | `i $xx` | 3 B | 3 |
| 3 | Indi.byte offs. | `IB` | `i [$xx]` | 3 B | 4 |
| 4 | Di.word | `DW` | `i $xxxx` | 4 B | 3 |
| 5 | Indi.word | `IW` | `i [$xxxx]` | 4 B | 4 |
| 6 | Di.reg./Di.reg. | `DR_DR` | `i r0, r1` | 2 B | 2 |
| 7 | Di.reg./Indi.reg. | `DR_IR` | `i r0, [r1]` | 2 B | 4 |
| 8 | Indi.reg./Indi.reg. | `IR_DR` | `i [r0], r1` | 2 B | 4 |
| 9 | Di.reg./Di.byte | `DR_DB` | `i r0, $xx` | 3 B | 3 |
| a | Di.reg./Indi.byte offs. | `DR_IB` | `i r0, [$xx]` | 3 B | 4 |
| b | Di.reg./Di.word | `DR_DW` | `i r0, $xxxx` | 4 B | 3 |
| c | Di.reg./Indi.word | `DR_IW` | `i r0, [$xxxx]` | 4 B | 4 |
| d | Indi.byte offs./Di.reg | `IB_DR` | `i [$xx], r0` | 3 B | 4 |
| e | Indi.word/Di.reg | `IW_DR` | `i [$xxxx], r0` | 4 B | 4 |
| f | - | - | - | - | - |

*Note: Indirect byte accesses (modes `7,b,e`) are PC relative. They allow accesses from `[PC-128]` to `[PC+127]`.*

- The `W` ('Word?') flag governs whether to act on 8-bit or 16-bit (word) operands.
  - On registers, only the lower 8 bits (7-0) will be used. This includes mode 2.
  - For memory accesses, only one byte (8 bits) will be loaded from, or stored to, the address in memory.
  - Assemblers should look for a `.B` suffix to the opcode to signify that `W=0`. An optional `.W` suffix might be used for clarity to signify `W=1`, although it is implied.

- The instructions follow Intel convention, that is: `OP TO, FROM`

- We notice that instructions using the implicit Void mode are only one byte long, even though the addressing mode field runs into byte 2 of the instruction. This is because the first four instructions use implicit mode *only*, so if bits 2-3 are `0`, we stop fetching bytes for the instruction.

Instructions
------------

| Op. | Mnemonic | Does... | Flags | Modes |
|-----|----------|-------- |-------|-------|
| `00`| `SEI` | `f |= $10` | `I` | - |
| `01`| `CLI` | `f &= ~$10`| `I` | - |
| `02`| `RTI` | `f = [s]; s += 2; p = [s]; s += 2` | `I N O C Z` | - |
| `03`| `RTS` | `p = [s]; s += 2` | - | - |
| `04`| `JP x` | `p = x` | -  | `012345` |
| `05`| `CL x` | `[s] = p; s -= 2; p = x` | - | `012345` |
| `06`| `JZ x` | `if (f & $1) then p = x` | - | `012345` |
| `07`| `CZ x` | `if (f & $1) then { [s] = p; s -= 2; p = x }` | - | `012345` |
| `08`| `JC x` | `if (f & $2) then p = x` | - | `012345` |
| `09`| `CC x` | `if (f & $2) then { [s] = p; s -= 2; p = x }` | - | `012345` |
| `0a`| `JO x` | `if (f & $4) then p = x` | - | `012345` |
| `0b`| `CO x` | `if (f & $4) then { [s] = p; s -= 2; p = x }` | - | `012345` |
| `0c`| `JN x` | `if (f & $8) then p = x` | - | `012345` |
| `0d`| `CN x` | `if (f & $8) then { [s] = p; s -= 2; p = x }` | - | `012345` |
| `0e`| `NOT x` | `x = ~x` | `N Z` | `012345` |
| `0f`| `INC x` | `x += 1` | `N O C Z` | `012345` |
| `10`| `DEC x` | `x -= 1` | `N O C Z` | `012345` |
| `11`| `IND x` | `x += 2` | `N O C Z` | `012345` |
| `12`| `DED x` | `x -= 2` | `N O C Z` | `012345` |
| `13`| `MV x, y` | `x = y` | `N Z` | `6789abcde` |
| `14`| `CMP x, y` | `temp = x - y` | `N O C Z` | `6789abcde` |
| `15`| `TST x, y` | `temp = x & y` | `N Z` | `6789abcde` |
| `16`| `ADD x, y` | `x += y` | `N O C Z` | `6789abcde` |
| `17`| `SUB x, y` | `x -= y` | `N O C Z` | `6789abcde` |
| `18`| `MUL x, y` | `x *= y` | `N O C Z` | `6789abcde` |
| `19`| `DIV x, y` | `x /= y` | `N O C Z` | `6789abcde` |
| `1a`| `LSL x, y` | `x <<= y` | `N C Z` | `6789abcde` |
| `1b`| `LSR x, y` | `(unsigned) x >>= y` | `N Z` | `6789abcde` |
| `1c`| `ASR x, y` | `(signed) x >>= y` | `N C Z` | `6789abcde` |
| `1d`| `AND x, y` | `x &= y` | `N Z` | `6789abcde` |
| `1e`| `OR x, y` | `x |= y` | `N Z` | `6789abcde` |
| `1f`| `XOR x, y` | `x ^= y` | `N Z` | `6789abcde` |

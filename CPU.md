CPU
===

Khepra has a fully 16-bit processor, with 8 registers, and a 64 KB address space.

Words are interpreted in little-endian ordering.

The CPU's clock frequency is `5.360520` MHz, which is `1/4` of the master (VPU) frequency; see [VPU.md](VPU.md) for more information.

Registers
---
There are 8 registers of 16-bit word size:

- `a, b, c, d, e` are general purpose registers
- ` p` is the Program counter register
- `s` is the Stack pointer register
- `f` is the Flags status register.

All registers may be used in instructions.

Flags register structure, bits 15-0 in order:

| `x x x x  x x x x` | | `x x x I  N O C Z` |
|-------------------|---|-------------------|

- `I` specifies whether to handle Interrupts
- `N` indicates a result which is Negative
- `O` indicates a result which cannot be represented as a *signed* 16-bit value (Overflow)
- `C` indicates a result which cannot be represented as an *unsigned* 16-bit value (Carry)
- `Z` indicates a result of Zero (0).

Instruction structure
---
Instructions are either `8`-bit, `16`-bit, `24`-bit or `32`-bit (`1`, `2`, `3` or `4` bytes) depending on their addressing mode.

Bytes 0, 1, 2, 3 in order:

|`C C C C  C W M M` | | `M M X X  X Y Y Y` | | `D D D D  D D D D` | | `D D D D  D D D D`|
|------------------|---|------------------|---|------------------|---|------------------|

- `C`: Opcode
- `W`: Byte/Word operands
- `M`: (Addressing) Mode
- `X`: Register X 
- `Y`: Register Y
- `D`: Immediate operand (Data)

Addressing modes
---

Their are 16 addressing modes, *plus* an implicit mode.

| Mode | Name | Short | Format | Instr. Length |
|------|------|-------|--------|---------------|
| - | Void | `V` | `i` | 1 B |
| 0 | Direct reg. | `DR` | `i r0` | 2 B |
| 1 | Indirect reg. | `IR` | `i [r0]` | 2 B |
| 2 | Di.byte | `DB` | `i $xx` | 3 B |
| 3 | Indi.byte offs. | `IB` | `i [$xx]` | 3 B |
| 4 | Di.word | `DW` | `i $xxxx` | 4 B |
| 5 | Indi.word | `IW` | `i [$xxxx]` | 4 B |
| 6 | Di.reg./Di.reg. | `DR_DR` | `i r0, r1` | 2 B |
| 7 | Di.reg./Indi.reg. | `DR_IR` | `i r0, [r1]` | 2 B |
| 8 | Indi.reg./Indi.reg. | `IR_DR` | `i [r0], r1` | 2 B |
| 9 | Di.reg./Di.byte | `DR_DB` | `i r0, $xx` | 3 B |
| a | Di.reg./Indi.byte offs. | `DR_IB` | `i r0, [$xx]` | 3 B |
| b | Di.reg./Di.word | `DR_DW` | `i r0, $xxxx` | 4 B |
| c | Di.reg./Indi.word | `DR_IW` | `i r0, [$xxxx]` | 4 B |
| d | Indi.byte offs./Di.reg | `IB_DR` | `i [$xx], r0` | 3 B |
| e | Indi.word/Di.reg | `IW_DR` | `i [$xxxx], r0` | 4 B |
| f | - | - | - | - |

*Note: Indirect byte accesses (modes `3,a,d`) are PC relative. They allow accesses from `[PC-128]` to `[PC+127]`.*

- The `W` ('Word?') flag governs whether to act on 8-bit or 16-bit (word) operands.
  - On registers, only the lower 8 bits (7-0) will be used. This includes mode 2.
  - For memory accesses, only one byte (8 bits) will be loaded from, or stored to, the address in memory.
  - Assemblers should look for a `.B` suffix to the opcode to signify that `W=0`. An optional `.W` suffix might be used for clarity to signify `W=1`, although it is implied.

- The instructions follow Intel convention, that is: `OP TO, FROM`

- We notice that instructions using the implicit Void mode are only one byte long, even though the addressing mode field runs into byte 2 of the instruction. This is because the first four instructions use implicit mode *only*, so if bits 2-3 are `0`, we stop fetching bytes for the instruction.

Instructions
------------

| Op. | Mnemonic | Does... | Flags | Modes | Clocks |
|-----|----------|-------- |-------|-------|--------|
| `00`| `NOP` | - | - | - | 2 |
| `01`| `INT` | `[s] = p; s -= 2; f |= $10; p = [$fffe]`| `I` | - | 4 |
| `02`| `RTI` | `f = [s]; s += 2; p = [s]; s += 2` | `I N O C Z` | - | 4 |
| `03`| `RTS` | `p = [s]; s += 2` | - | - | 3 |
| `04`| `JP x` | `p = x` | -  | `012345` | 2, 4, 3, 4, 3 |
| `05`| `CL x` | `[s] = p; s -= 2; p = x` | - | `012345` | 2, 4, 3, 4, 3 |
| `06`| `JZ x` | `if (f & $1) then p = x` | - | `012345` | 2, 4, 3, 4, 3 |
| `07`| `CZ x` | `if (f & $1) then { [s] = p; s -= 2; p = x }` | - | `012345` | 2, 4, 3, 4, 3 |
| `08`| `JC x` | `if (f & $2) then p = x` | - | `012345` | 2, 4, 3, 4, 3 |
| `09`| `CC x` | `if (f & $2) then { [s] = p; s -= 2; p = x }` | - | `012345` | 2, 4, 3, 4, 3 |
| `0a`| `JO x` | `if (f & $4) then p = x` | - | `012345` | 2, 4, 3, 4, 3 |
| `0b`| `CO x` | `if (f & $4) then { [s] = p; s -= 2; p = x }` | - | `012345` | 2, 4, 3, 4, 3 |
| `0c`| `JN x` | `if (f & $8) then p = x` | - | `012345` | 2, 4, 3, 4, 3 |
| `0d`| `CN x` | `if (f & $8) then { [s] = p; s -= 2; p = x }` | - | `012345` | 2, 4, 3, 4, 3 |
| `0e`| `NOT x` | `x = ~x` | `N Z` | `012345` | 2, 4, 3, 4, 3 |
| `0f`| `INC x` | `x += 1` | `N O C Z` | `012345` | 2, 4, 3, 4, 3 |
| `10`| `DEC x` | `x -= 1` | `N O C Z` | `012345` | 2, 4, 3, 4, 3 |
| `11`| `IND x` | `x += 2` | `N O C Z` | `012345` | 2, 4, 3, 4, 3 |
| `12`| `DED x` | `x -= 2` | `N O C Z` | `012345` | 2, 4, 3, 4, 3 |
| `13`| `MV x, y` | `x = y` | `N Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `14`| `CMP x, y` | `temp = x - y` | `N O C Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `15`| `TST x, y` | `temp = x & y` | `N Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `16`| `ADD x, y` | `x += y` | `N O C Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `17`| `SUB x, y` | `x -= y` | `N O C Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `18`| `MUL x, y` | `x *= y` | `N O C Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `19`| `DIV x, y` | `x /= y` | `N O C Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `1a`| `LSL x, y` | `x <<= y` | `N C Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `1b`| `LSR x, y` | `(unsigned) x >>= y` | `N Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `1c`| `ASR x, y` | `(signed) x >>= y` | `N C Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `1d`| `AND x, y` | `x &= y` | `N Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `1e`| `OR x, y` | `x |= y` | `N Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |
| `1f`| `XOR x, y` | `x ^= y` | `N Z` | `6789abcde` | 2, 4, 4, 3, 4, 3, 4, 4, 4 |

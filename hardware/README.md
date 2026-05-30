# Hardware — Custom AFE + PCB (Hardware track)

Multi-channel analog front-end: instrumentation amp → HPF (~20 Hz) → LPF (~500 Hz)
→ rectifier → envelope. KiCad. 2-layer board.

## Sequence (do NOT skip the order)
1. Design schematic (Act 1, on paper / KiCad)
2. **Breadboard block-by-block, verify each stage on the scope** (Act 2, in person)
3. Lay out PCB — **post to r/PrintedCircuitBoard for review BEFORE fabbing**
4. Fab (JLCPCB), assemble, verify matches breadboard

## Multi-channel
Prove ONE discrete channel first. Then replicate, or move to an ADS1299
(8-ch, 24-bit, simultaneous sampling) read by the STM32 over SPI.

## Files
- `kicad/` — schematic + PCB layout
- `BOM.md` — bill of materials (fill in as you finalize parts)

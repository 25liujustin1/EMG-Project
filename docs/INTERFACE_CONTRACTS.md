# Interface Contracts

> **This is the most important document in the repo.** These contracts are what
> let the two tracks be built independently and combined cleanly. Fill them in
> (and agree on them, both partners) in Phase 0, *before* writing any code that
> depends on them. Treat changes here as breaking changes — discuss before editing.

There are two convergence points in the project, and each needs a contract:

1. **UART bridge** — STM32 firmware ↔ FPGA (and ↔ PC tooling)
2. **ML → hardware weight handoff** — the trained model → on-device classifier

---

## Contract 1 — UART data format

The format STM32 firmware transmits and that the FPGA UART receiver (and the PC
visualizer) must decode. Everyone builds to THIS, not to each other's code.

### Fill these in (decide together):

| Parameter | Value | Notes |
|---|---|---|
| Baud rate | `_______` | e.g. 115200 (verify the FPGA can sample it reliably) |
| Bits per sample | `_______` | e.g. 12 (raw ADC) or 16 (padded) |
| Channel count | `_______` | start at 1, plan for N |
| Sample rate (per channel) | `_______ Hz` | EMG needs ~1–2 kHz; verify UART bandwidth fits N channels |
| Byte order | `_______` | little- or big-endian |
| Frame structure | see below | how a packet is delimited |
| Checksum / sync | `_______` | how the receiver recovers from a dropped byte |

### Frame structure (template — adapt):

```
[ SYNC byte(s) ] [ optional seq/timestamp ] [ ch0 sample ] [ ch1 sample ] ... [ chN ] [ checksum ]
```

Define exactly:
- **SYNC pattern:** `__________` (a fixed byte/sequence the receiver locks onto)
- **Sample encoding:** `__________` (how each N-bit sample maps to bytes)
- **Packet length:** `__________` bytes
- **Sync recovery rule:** `__________` (what the receiver does on a bad checksum)

### Bandwidth sanity check (do the math here):

```
required_baud ≈ sample_rate × channels × bits_per_sample × (10/8 framing overhead) × safety
```
Fill in: `_______` → confirm it's below your chosen baud rate. If not, raise the
baud, drop channels, or move to a faster link (USB).

### Notes / decisions:
- (record any choices and why here)

---

## Contract 2 — ML → hardware weight handoff

The format in which the trained, quantized model is handed from `ml/` to the
on-device classifier (STM32 TFLite Micro and/or the on-FPGA SystemVerilog MAC datapath).

### Fill these in (decide together):

| Parameter | Value | Notes |
|---|---|---|
| Model type | `_______` | linear classifier / small MLP (FPGA-feasible) |
| Input features | `_______` | which features feed the classifier (e.g. FFT band powers) |
| Feature count | `_______` | input vector length |
| Output classes | `_______` | number of gestures |
| Hidden layer size | `_______` | if MLP; 0 if linear |
| Quantization | `_______` | e.g. int8 |
| Fixed-point format | `_______` | total bits / fractional bits (e.g. Q8.8) |
| Weight file format | `_______` | how weights are stored (see below) |

### Fixed-point format (the critical detail for the FPGA classifier):

Define exactly how a real-valued weight maps to an integer:
- **Total bits:** `_______`
- **Integer bits:** `_______`
- **Fractional bits:** `_______`
- **Signed/unsigned:** `_______`
- **Rounding / saturation rule:** `_______`

The FPGA datapath implements arithmetic in THIS format. The ML side must produce
weights already quantized to it.

### Weight file layout (template):

```
# one weight per line, or a structured file (JSON/CSV/hex) — decide and document
# include: layer, shape, quantization params, then the integer weight values
```

Specify:
- **File format:** `__________` (JSON / CSV / hex / $readmemh for Verilog)
- **Ordering:** `__________` (how weights map to MAC units — row-major? per-neuron?)
- **Bias handling:** `__________`

### Notes / decisions:
- (record any choices and why here)

---

## Change log for these contracts

| Date | Change | Agreed by |
|---|---|---|
| | initial draft | |

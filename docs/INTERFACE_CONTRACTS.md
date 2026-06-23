# Interface Contracts

## UART data format

| Parameter | Value | Notes |
|---|---|---|
| Baud rate | `_______` | e.g. 115200 (verify the FPGA can sample it reliably) |
| Bits per sample | `_______` | e.g. 12 (raw ADC) or 16 (padded) |SS
| Channel count | `_______` | start at 1, plan for 8 |
| Sample rate (per channel) | `_______ Hz` | EMG needs ~1–2 kHz; verify UART bandwidth fits N channels |
| Byte order | `_______` | little- or big-endian |
| Frame structure | see below | how a packet is delimited |
| Checksum / sync | `_______` | how the receiver recovers from a dropped byte |

Additional:
- **SYNC pattern:** `__________` (a fixed byte/sequence the receiver locks onto)
- **Sample encoding:** `__________` (how each N-bit sample maps to bytes)
- **Packet length:** `__________` bytes
- **Sync recovery rule:** `__________` (what the receiver does on a bad checksum)


---

## ML hardware weight handoff

The format in which the trained, quantized model is handed from `ml/` to the
on-device classifier (STM32 TFLite Micro and/or the on-FPGA SystemVerilog MAC datapath).


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

### Fixed-point format

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
# one weight per line, or a structured file (JSON/CSV/hex)
# include: layer, shape, quantization params, then the integer weight values
```

Specify:
- **File format:** `__________` (JSON / CSV / hex / $readmemh for Verilog)
- **Ordering:** `__________` (how weights map to MAC units — row-major? per-neuron?)
- **Bias handling:** `__________`

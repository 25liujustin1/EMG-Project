# FPGA — SystemVerilog (Hardware track)

Digital signal processing and (stretch) on-FPGA ML inference. Tang Nano 20K, Gowin
official IDE, SystemVerilog. **Simulation-first** — verify in sim before hardware.

## Modules (per roadmap MODULE A2 / C1 / C3)
- `alu/`            — mini ALU (learning)
- `uart_receiver/`  — UART RX, the bridge from STM32  ← CORE FPGA TARGET
- `fft/`            — FFT engine (REACH)
- (later) classifier — fixed-point MAC datapath (FULL)

## Testbenches
Each module has a `tb/` folder. **A testbench is an async contract** — the other
partner can verify a module against its testbench with no hardware present.

## Validate against the reference
The HW FFT is checked against the reference Python FFT in `ml/` (the source of truth).

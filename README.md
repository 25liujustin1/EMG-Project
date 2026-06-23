# EMG Biosignal System


## Repo structure

| Folder | Contents | Owner |
|---|---|---|
| `docs/` | Roadmap, **interface contracts**, decisions log, datasheets | Shared |
| `firmware/` | STM32 bare-metal C (acquisition, DMA, UART) | Hardware |
| `fpga/` | SystemVerilog + testbenches (UART RX, FFT, classifier) | Hardware |
| `hardware/` | KiCad schematic, PCB layout, BOM (custom AFE) | Hardware |
| `ml/` | Training, models, feature pipeline | Software |
| `tools/` | Live visualizer, data-capture tool, STM32 emulator | Software |
| `data/` | Captured EMG (small) + scripts to fetch Ninapro (NOT committed) | Shared |
| `integration/` | Glue scripts/configs that wire the tracks together | Shared |

## Ownership split

- **Hardware track:** `firmware/`, `fpga/`, `hardware/`
- **Software track:** `ml/`, most of `tools/`
- **Shared:** `docs/`, `integration/`, `data/`

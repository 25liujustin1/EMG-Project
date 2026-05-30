# Tools — Shared software (mostly Software track)

- **Live visualizer** — real-time time-domain + spectrum + classifier output
- **Data-capture & labeling tool** — record sessions, tag gestures, export datasets
- **STM32 emulator** — fakes/replays EMG over a virtual serial port so the whole
  pipeline is testable with NO hardware (unblocks Act 1 ML work)

All should speak the UART format in `docs/INTERFACE_CONTRACTS.md` (Contract 1) so
they work identically against the emulator and the real STM32.

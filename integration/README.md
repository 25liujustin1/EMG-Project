# Integration — the glue (Shared)

Scripts/configs that wire the tracks together at the two convergence points:

1. **UART bridge** — firmware ↔ fpga. Verify with the logic analyzer against
   Contract 1. Bring-up happens in Act 2 (in person).
2. **Weight handoff** — ml → fpga/firmware. The trained model's quantized weights
   (Contract 2) get consumed by the on-device classifier.

Put end-to-end test scripts and the full-chain launch configs here.

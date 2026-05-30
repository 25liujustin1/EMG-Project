# Decisions Log

A running record of significant choices and *why* we made them. When you're deep
in week 8 and ask "wait, why did we decide that?", this is the answer. Add a row
whenever you make a real decision. Revisit entries marked "revisit" if time allows.

| Date | Decision | Rationale | Revisit? |
|---|---|---|---|
| | Custom AFE (build + PCB) | Scarce analog/PCB skill; the differentiating hardware bullet | — |
| | Buying an oscilloscope | Custom AFE bring-up needs one; checking lab access first | if lab access found |
| | Full FPGA ambition (on-FPGA ML classifier) | Headline goal; STM32 TFLite is the fallback | — |
| | Multi-channel | More gestures / finer resolution | prove 1 channel first |
| | SystemVerilog in Gowin official IDE | Roadmap is in SV; official IDE has best SV support on Gowin | — |
| | Keep STM32; ADS1299 as multi-channel ADC later | STM32 = controller/streamer; ADS1299 = simultaneous-sample 24-bit | — |
| | | | |

## Open questions (resolve as you go)

- [ ] Multi-channel via discrete AFE channels or ADS1299? (lean: prove 1 discrete channel, then decide)
- [ ] Oscilloscope: lab access vs. buy vs. buy-in-China?
- [ ] One Tang Nano or two (for full async)?
- [ ] Add an IMU (MPU6050) to boost gesture accuracy + enable regression? (cheap, ~$5–15)

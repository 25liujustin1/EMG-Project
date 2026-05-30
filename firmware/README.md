# Firmware — STM32 (Hardware track)

Bare-metal C for multi-channel EMG acquisition and streaming.

## Modules (per roadmap MODULE A1)
- GPIO / blinky
- UART TX/RX (retarget printf)
- SysTick timers
- Interrupts / NVIC
- ADC (basic → DMA → multi-channel)
- (later) SPI master to ADS1299 for multi-channel acquisition

## Output
Streams samples over UART per the format in `docs/INTERFACE_CONTRACTS.md` (Contract 1).

## Test without an AFE
Use a potentiometer or the STM32 emulator in `tools/` as a substitute input.

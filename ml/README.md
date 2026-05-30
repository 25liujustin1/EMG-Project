# ML — Gesture classifier (Software track)

Trains a model that maps EMG features → gesture label. Develops on Ninapro first
(unblocked, day one), retrains on our own captured data once hardware exists.

## Pipeline (per roadmap MODULE A4 / C4 / C5)
- `pipeline/` — feature extraction (RMS, ZC, waveform length, MAV, FFT band powers)
- `models/`   — SVM, random forest, MLP, 1D CNN
- Deployment: quantize → STM32 TFLite Micro (reliable) and/or weights for the
  on-FPGA classifier (per Contract 2 in `docs/INTERFACE_CONTRACTS.md`)

## Reference DSP
Keep a clean Python FFT/feature pipeline here — it's the SOURCE OF TRUTH the
hardware FFT is validated against.

## Robustness wins (cheap, high-impact)
Rest/null class, temporal smoothing, uncertainty thresholding, per-user calibration.

## Data
Don't commit Ninapro (huge). Use `data/fetch_ninapro.sh`. Commit only small own data.

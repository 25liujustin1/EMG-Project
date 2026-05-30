EMG BIOSIGNAL SYSTEM — PROJECT ROADMAP
========================================

Multi-channel surface-EMG gesture recognition system: custom analog front-end →
STM32 acquisition → FPGA digital signal processing (FFT) → machine-learning
classification, with on-FPGA inference as the headline goal.

Two people. Full ambitious scope (custom AFE + on-FPGA ML + multi-channel).

---

TIMELINE OVERVIEW
=================

Total: June 11 – Sept 24  (~15 weeks)

The summer splits naturally into THREE ACTS around the in-person window. This
structure drives the entire plan: do solo/simulatable work apart, save the
hard physical work for when you are together.

  ACT 1 — APART        Jun 11 – Jul 10   (~4 weeks)
                       Parallel solo foundations. Sim-first. Arrive at the
                       in-person window with pieces READY TO COMBINE.

  ACT 2 — TOGETHER     Jul 10 – Aug 1    (3 weeks)  *** THE POWER WINDOW ***
                       The hard physical convergence: AFE bring-up, multi-channel
                       hardware, STM32<->FPGA integration. Things that are
                       miserable apart and great together. Protect this window.

  ACT 3 — APART        Aug 1 – Sep 24    (~8 weeks)
                       ML deepening, on-FPGA classifier, polish, stretch goals.
                       Mostly software / individual work that survives separation.

GUIDING PRINCIPLE: There is a complete, demoable artifact at the end of every
phase. If the summer runs short, you STOP at a phase boundary with something
whole — never a half-integrated mess.

OWNERSHIP:
  YOU (hardware) ...... firmware/, fpga/, hardware/ (AFE + PCB)
  FRIEND (software) ... ml/, most of tools/
  SHARED .............. docs/, integration/, data/

---

SCOPE TIERS (read this before every decision)
==============================================

CORE (must hit — the project is "done" and defensible at this line):
  * STM32 multi-channel acquisition working
  * Custom AFE built and producing clean EMG (at least 1 channel solid)
  * FPGA through the UART receiver, verified in simulation
  * ML pipeline classifying gestures well on real captured data
  * ONE end-to-end path working (even if classification runs on the PC)

REACH (the strong version — aim here if Act 1 & 2 go well):
  * Multi-channel AFE on custom PCB
  * FFT engine running in FPGA hardware
  * ML model deployed on STM32 (TFLite Micro) — "ML on hardware we built"
  * Live multi-channel classification, more gestures

FULL (the headline ambition — the stretch-of-stretch):
  * On-FPGA ML classifier in SystemVerilog (hand-built fixed-point MAC datapath)
  * Multi-label / many-gesture decoding
  * Polished real-time demo

FALLBACK LADDER (so nothing is all-or-nothing):
  * On-FPGA classifier doesn't fit?  -> STM32 TFLite Micro deployment still counts.
  * FFT engine in HW runs out of time? -> FFT verified in simulation still counts.
  * Custom AFE fights you?            -> substitute input (potentiometer / Ninapro)
                                         keeps the whole digital chain moving.
  * Multi-channel too much?           -> single channel is a complete system.

---

PHASE 0 — PRE-SUMMER / WEEK 1 SETUP  (do NOW / first days)
==========================================================
Goal: remove lead-time risk and set the foundation so Act 1 starts clean.

Ordering & logistics (do first — things have shipping lag):
  [ ] Order 2x STM32 boards
  [ ] Order 1-2x Tang Nano 20K
  [ ] Order AFE components (INA333 + breakouts, op-amps, R/C assortment kits, spares)
  [ ] Order multi-channel parts (extra AFE channels OR plan an ADS1299 — see note)
  [ ] Order EMG gel electrodes (lots) + leads
  [ ] BUY THE OSCILLOSCOPE — needed for AFE bring-up in Act 2. Don't wait.
      (USB scope ~$100-150 budget option, or Rigol DS1054Z ~$349; verify mains
       voltage if buying abroad.)
  [ ] Order a cheap USB logic analyzer (~$15) for UART debugging
  [ ] Soldering gear (iron, flux, fine solder, helping hands) for SMD breakout work

Foundation:
  [ ] Create the shared Git repo (structure below)
  [ ] BOTH do HDLBits for a weekend — the go/no-go test for the FPGA track
  [ ] Write the INTERFACE CONTRACTS in docs/ (see below) BEFORE writing dependent code
  [ ] Decide toolchain: Gowin official IDE (best SystemVerilog support), SV language

NOTE on multi-channel: Decide early whether multi-channel = multiple discrete AFE
channels (simpler analog, more board space) or an ADS1299-class chip (8 channels,
24-bit, simultaneous sampling, one part — but more complex to bring up). For a
FIRST custom analog board, recommend: prove ONE discrete channel first, then
replicate to a few channels. Treat ADS1299 as the REACH/FULL multi-channel path.

REPO STRUCTURE:
  project-root/
    README.md            <- the map + current status
    docs/                <- roadmap, INTERFACE CONTRACTS, decisions log, datasheets
    firmware/            <- STM32 bare-metal C
    fpga/                <- SystemVerilog + testbenches (tb/ alongside each module)
    ml/                  <- training, models, pipeline
    tools/               <- visualizer, data-capture tool, STM32 emulator
    data/                <- captured EMG (small) + scripts to fetch Ninapro (don't commit it)
    hardware/            <- KiCad schematic, PCB, BOM
    integration/         <- glue scripts/configs wiring tracks together

INTERFACE CONTRACTS (the highest-value document — write these day one):
  1. UART data format: sample rate, bits/sample, channel count, framing,
     packet structure, checksum. Both firmware/ and fpga/ build to THIS.
  2. ML<->hardware weight handoff: fixed-point format (integer/fraction bits),
     weight table layout. ml/ produces it, fpga/ (or firmware/) consumes it.

---

ACT 1 — APART  (Jun 11 – Jul 10, ~4 weeks)
===========================================
Goal: maximize independent, simulatable progress so you walk into the in-person
window with components ready to combine. Nothing here needs you co-located.

------------------------------------------------------------
YOUR TRACK (hardware) — Act 1
------------------------------------------------------------

MODULE A1 — STM32 firmware foundations  (Standalone: yes)
  Stage 0  GPIO / blinky (RCC, MODER, ODR)
  Stage 1  UART TX to PC, retarget printf, then RX
  Stage 2  SysTick timer, precise delays
  Stage 3  Interrupts / NVIC
  Stage 4  ADC basic — read a potentiometer, stream raw values to PC, plot in Python
  Stage 5  ADC + DMA — continuous sampling, stream buffer, verify no dropped samples
  Stage 6  MULTI-CHANNEL ADC — sample multiple channels, agree on the UART contract
  Substitute for downstream: potentiometer input until the AFE exists.
  Milestone: clean multi-channel digital samples streaming over UART.

MODULE A2 — FPGA simulation foundations  (Standalone: yes, sim-only)
  Stage 0  HDLBits (finish it) — SV basics: logic, always_comb/always_ff, blocking vs not
  Stage 1  Mini ALU + testbench (always_comb, case, mux)
  Stage 2  Parameterized clock divider, stopwatch
  Stage 3  FSMs — LFSR, draw state diagrams first
  Stage 4  UART RECEIVER in SystemVerilog + testbench  <-- CORE FPGA TARGET
           (This is the bridge to your STM32. A testbench here = async contract.)
  Milestone: UART receiver verified in simulation. Everything past this is REACH.

MODULE A3 — AFE design on paper  (Standalone: yes, no components)
  Stage 0  Theory: why instrumentation amp, CMRR, EMG band (20-500 Hz)
  Stage 1  Design HPF (~20 Hz) + LPF (~500 Hz) on paper; precision rectifier;
           RC envelope; compute all R/C values (TI FilterPro)
  Stage 2  Single-channel schematic in KiCad; plan multi-channel replication
  Resources: TI instrumentation-amp app notes; ADI Op Amp Applications Handbook
  Milestone: full single-channel schematic ready to breadboard in Act 2.

(Optional, if parts arrive early & you're ahead: start breadboarding ONE channel
 solo. But the real breadboard push is Act 2 with the scope + your friend.)

------------------------------------------------------------
FRIEND'S TRACK (software / ML) — Act 1  (fully unblocked, day one)
------------------------------------------------------------

MODULE A4 — ML pipeline on public data  (Standalone: yes, Ninapro)
  Stage 1  Load Ninapro; feature engineering (RMS, ZC, waveform length, MAV,
           FFT band powers); train/test/subject splits
  Stage 2  Model comparison: SVM, random forest, small MLP; confusion matrices
  Stage 3  Start Tier 2: 1D CNN on raw windows
  Milestone: a working, evaluated gesture classifier on Ninapro.

MODULE A5 — Shared tooling  (Standalone: yes)
  [ ] Live signal visualizer (time-domain + spectrum + classifier output)
  [ ] Data-capture & labeling tool (records sessions, tags gestures, exports)
  [ ] STM32 EMULATOR — fakes/replays EMG over a virtual serial port so the
      whole pipeline is testable with NO hardware. (Unblocks everything.)
  [ ] Reference Python DSP pipeline = the "source of truth" for the HW FFT later
  Milestone: tooling ready so that when real hardware arrives, it plugs in.

ACT 1 EXIT CRITERIA (what "ready for the power window" looks like):
  * STM32 streams multi-channel samples (on potentiometer/test input)
  * FPGA UART receiver verified in sim
  * AFE schematic complete, components in hand
  * ML pipeline working on Ninapro; tooling + emulator ready
  * Interface contracts written and agreed

---

ACT 2 — TOGETHER  (Jul 10 – Aug 1, 3 weeks)  *** PROTECT THIS WINDOW ***
========================================================================
Goal: do the work that is HARD APART and GREAT TOGETHER. Two brains, one scope,
one bench. This is where the physical system comes alive. Prioritize ruthlessly —
if something can be done solo in Act 3, don't spend the window on it.

WEEK 1 of window — AFE BREADBOARD BRING-UP (the riskiest physical work)
  [ ] Breadboard ONE channel, BLOCK BY BLOCK, verifying each on the scope:
      instrumentation amp -> HPF -> LPF -> rectifier -> envelope
  [ ] Test with potentiometer FIRST (known input), electrodes LAST
  [ ] Fight the noise (60 Hz hum): grounding, short leads, shielding, battery power
  [ ] Confirm real EMG: flex muscle, see response on scope
  [ ] Connect AFE output -> STM32 ADC; capture live EMG; verify in Python
  Milestone: ONE clean channel of real EMG captured end to end.

WEEK 2 of window — MULTI-CHANNEL + STM32<->FPGA INTEGRATION
  [ ] Replicate to multiple channels (discrete) OR bring up ADS1299
  [ ] Multi-channel capture, verify all channels in the visualizer
  [ ] STM32 <-> FPGA UART bridge: stream live data, verify with logic analyzer
      against the interface contract (this is where the contract pays off)
  [ ] Capture YOUR OWN labeled multi-channel EMG dataset (many gestures, many
      reps, varied speeds) — friend's pipeline retrains on this in Act 3
  Milestone: multi-channel real EMG flowing STM32 -> FPGA; own dataset captured.

WEEK 3 of window — PCB + FIRST HARDWARE FPGA
  [ ] Finalize multi-channel AFE PCB layout in KiCad
  [ ] *** Post layout to r/PrintedCircuitBoard for review BEFORE fabbing ***
  [ ] Order PCB (JLCPCB) — budget for ONE revision; it ships during Act 3
  [ ] Flash Tang Nano: blinky -> port ALU/FSM -> get UART receiver on real HW
  [ ] (If ahead) begin FFT engine in simulation
  Milestone: PCB ordered; FPGA running real designs on hardware.

ACT 2 EXIT CRITERIA:
  * Multi-channel real EMG captured and verified
  * STM32<->FPGA talking on real hardware
  * Your own labeled dataset in hand
  * Custom PCB designed, reviewed, and ordered
  * FPGA basics running on the Tang Nano

(If the window is tight, the must-dos are: AFE bring-up, your own dataset capture,
 and the UART bridge. PCB and hardware-FPGA can slip to Act 3 / remote.)

---

ACT 3 — APART  (Aug 1 – Sep 24, ~8 weeks)
==========================================
Goal: deepen the ML, build toward the on-FPGA classifier, assemble & validate the
PCB, and polish. Most of this survives separation. Designate a "hardware host"
(whoever keeps the bench/scope) for the physical bits; the other supports remotely.

------------------------------------------------------------
YOUR TRACK (hardware) — Act 3
------------------------------------------------------------

MODULE C1 — FFT engine in hardware  (REACH)
  [ ] Finish FFT engine in SystemVerilog (twiddle factors, fixed-point, butterflies)
      — validate against friend's reference Python FFT (the source of truth)
  [ ] Feed live EMG through HW FFT; output dominant band
  Fallback: FFT verified in simulation still counts as CORE.

MODULE C2 — PCB assembly & validation  (REACH)
  [ ] Assemble the fabbed PCB; verify it matches the breadboard on the scope
  [ ] Expect to debug: bad joints, swap values, cut traces, bodge wires (normal!)
  [ ] Roll validated fixes into a revision only if needed
  Fallback: breadboard AFE keeps the system working.

MODULE C3 — On-FPGA ML classifier  (FULL — the headline stretch)
  [ ] Friend hands over quantized int8 weights + fixed-point format (the contract)
  [ ] Build MAC datapath + activation + argmax + control FSM in SystemVerilog
  [ ] Wire it downstream of the FFT/feature stage; verify in sim, then on HW
  Fallback: STM32 TFLite Micro deployment (Module C5) is the reliable "ML on
  hardware" story if the RTL classifier runs out of runway.

------------------------------------------------------------
FRIEND'S TRACK (software / ML) — Act 3
------------------------------------------------------------

MODULE C4 — Train on your own data + go deeper  (REACH)
  [ ] Retrain the pipeline on YOUR captured multi-channel dataset
      (keep Ninapro as fallback; consider pretrain-on-Ninapro + fine-tune)
  [ ] Multi-channel features / spatial filtering for more gestures
  [ ] Add: rest/null class, temporal smoothing, uncertainty thresholding,
      per-user calibration (cheap, high-impact robustness wins)
  [ ] Use the confusion matrix to iterate on the gesture set

MODULE C5 — Deployment  (REACH -> enables FULL)
  [ ] Quantize chosen model to int8; deploy on STM32 via TFLite Micro (reliable path)
  [ ] Produce the weight table + format for the on-FPGA classifier (-> Module C3)
  Milestone: a trained model running in real time on hardware you built.

MODULE C6 — Demo & polish  (the storytelling layer)
  [ ] Polished real-time demo UI (the difference between "works" and "wow")
  [ ] Optional: gesture-controlled application / actuator demo
  [ ] Documentation: README, architecture diagrams, results, confusion matrices,
      demo video, project writeup (matters for résumé/portfolio)

ACT 3 EXIT CRITERIA (the FULL system):
  * Multi-channel custom PCB assembled and working
  * FFT engine on hardware
  * ML trained on your own data, deployed on hardware (STM32 and/or FPGA)
  * End-to-end demo: flex -> multi-channel EMG -> FFT -> classify -> output
  * Documented and demoable

---

THE TWO CONVERGENCE POINTS (where the tracks MUST sync)
=======================================================
1. UART BRIDGE — firmware <-> fpga, governed by the UART interface contract.
   Verified by shared testbenches (async) + on real HW during Act 2 (together).
2. WEIGHT HANDOFF — ml -> fpga/firmware, governed by the fixed-point format contract.
   ml/ produces the weight table; you consume it in C3/C5.
Write BOTH contracts in docs/ in Phase 0. They are what let you work apart.

---

TOP RISKS & MITIGATIONS (the things most likely to bite)
========================================================
1. FPGA learning curve (first Verilog, longest track)
   -> HDLBits before summer; core = UART receiver; FFT/classifier are stretch.
2. Custom AFE (un-simulatable, noise-prone, high variance)
   -> breadboard block-by-block in Act 2 with scope + two brains; substitute input
      keeps the digital chain moving; budget a PCB revision.
3. Multi-channel adds scope -> prove ONE channel first, then replicate.
4. Integration (always underestimated) -> interface contracts + the in-person window.
5. On-FPGA classifier (stretch-of-stretch) -> STM32 TFLite Micro is the fallback.
6. Scope creep -> lock CORE first; everything else is explicitly labeled stretch.
7. Hardware lead time -> order everything in Phase 0; PCB ships during Act 3.

---

WEEK-BY-WEEK SKELETON (rough — adjust as you go)
================================================
  Wk 1   (Jun 11) Phase 0: order parts, repo, contracts, HDLBits
  Wk 2-4 (Jun)    Act 1: STM32 firmware + FPGA sim + AFE schematic // ML + tooling
  Wk 5   (Jul 10) Act 2 begins — AFE breadboard bring-up
  Wk 6   (Jul)    Act 2 — multi-channel + STM32<->FPGA bridge + dataset capture
  Wk 7   (Jul/Aug)Act 2 — PCB design+order, FPGA on hardware
  Wk 8   (Aug 1)  Act 3 begins — apart again
  Wk 8-10         FFT engine in HW // retrain on own data, deepen ML
  Wk 11-12        PCB assembly+validation // deployment (STM32 TFLite)
  Wk 13-14        On-FPGA classifier // demo UI, calibration, more gestures
  Wk 15  (Sep 24) Polish, documentation, demo video, final integration

---

REMEMBER
========
* Finished beats ambitious. A complete CORE system you can demo > a half-built
  FULL one. Climb toward FULL, but always stand on finished ground.
* Stop at any phase boundary with something whole.
* The in-person window is your scarcest resource — spend it on what's impossible
  apart (AFE bring-up, integration, dataset capture), not on solo-able work.
* Write the interface contracts FIRST. They are what make "two people apart" work.

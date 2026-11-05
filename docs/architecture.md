# Architecture

## System Overview

The stopwatch was implemented as a hierarchy of reusable RTL modules rather than as one monolithic block.

### Main Blocks

- **FA** - one-bit full adder used as a basic arithmetic building block.
- **CSA** - parameterized conditional-sum adder constructed hierarchically from smaller arithmetic blocks.
- **Lim_Inc** - limited incrementer that increments until a configurable limit and then wraps to zero while generating carry-out.
- **Counter** - synchronous stopwatch counter. A hierarchy of limited incrementers divides the FPGA clock and maintains the displayed time.
- **Ctl** - synchronous control FSM implementing the stopwatch operating states and generating initialization/count-enable control signals.
- **Debouncer** - converts a mechanical push-button input into a stable one-cycle event.
- **Stash** - register-based sample storage. New stopwatch readings can be captured and later browsed without removing them from storage.
- **Seg_7_Display** - time-multiplexed four-digit seven-segment display driver.
- **Stopwatch** - top-level integration block connecting button inputs, control, counter, stash, display selection, LEDs, and seven-segment outputs.

## Control States

The control logic uses three main states:

- **IDLE** - stopwatch initialized / not actively counting.
- **COUNTING** - counter enabled and stopwatch time advancing.
- **PAUSED** - counting disabled while the current time is held.

Button events are debounced before entering the control logic. The top-level also maps buttons differently depending on whether the user is viewing the stopwatch or the stored-sample mode.

## Split / Freeze Behavior

A split event captures the current stopwatch reading into a register so the displayed value can remain frozen while the internal stopwatch continues operating. Display-selection logic chooses between the live time, frozen time, and stash output.

## Sample Stash

The stash stores captured 8-bit stopwatch samples in a fixed-depth register array. New samples can overwrite the oldest entries when the storage depth is exceeded, while a selection pointer allows stored values to be browsed on the display.

## Board Interface

The Basys-3 board provides:

- push buttons for reset, trigger, split, mode selection, sampling, and stored-sample navigation;
- LEDs indicating the selected display mode;
- a four-digit seven-segment display for stopwatch and stored-sample output;
- the FPGA system clock used by the counter and display-refresh logic.

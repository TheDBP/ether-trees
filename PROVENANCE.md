# nextlit @ master

A mirror of someone else's work. Nothing here is ours and nothing here should be edited.

## Where it came from

    upstream   github.com/biqqles/nextlit
    branch     master
    commit     a592612ab4566e893f1efdfa4dfa847a8020b3ce
    licence    Mozilla Public License 2.0
    state      archived by its author, last pushed 2021-03-02

## Why it is mirrored

It is archived, and archived repositories can still be deleted by their owner. It is also the only
substantial public documentation of how the Nextbit Robin's segmented rear LEDs are actually driven,
and it was found by accident.

## What it documents

The rear LEDs are an LP5523 with three programmable engines. The controls are **not** in the per-LED
directories, which is why they are easy to miss:

    /sys/class/leds/lp5523:channel0/device/
        engine1_leds  engine1_load  engine1_mode   (and 2, 3)
        run_engine    select_engine  led_pattern
    /sys/class/leds/nbq_wled/brightness

`led_pattern` selects one of five patterns Nextbit programmed into the chip. The engines run
bytecode, so animation happens in hardware and continues while the AP sleeps. From its source:

    98d0   start, load multiplexer register
    9d0x   select one LED to be controlled (x = count of the LED in the mux)
    9d00   clear engine-to-output mapping
    40xx   set brightness of all LEDs the engine controls
    xxyy   ramp brightness over time: time per step, number of steps

That last instruction is the fade primitive. Mux ordering is right-to-left, and `111100000` gives an
engine all four LEDs.

Two traps its author recorded, both worth keeping:

- **Engine 1 is unreliable**, apparently because it runs the predefined patterns, one of which
  plays at boot on most ROMs. Prefer engines 2 and 3.
- Sequences with no `9d0x` (mux select) silently refuse to run if executed any time after predefined
  pattern 5, unless they include `9d00` (mux clear). With `98d0` and `0000` also mandatory, the
  usable budget is about 14 instructions per engine.

## Licence

MPL-2.0. Redistribution is permitted with the licence retained; `LICENSE.md` is present unmodified.
Mirrored, not forked: no modifications, and no intent to maintain it.

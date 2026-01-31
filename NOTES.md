# Lab Temperature Calculator

## What it does

Single HTML file calculator for Fran's lab. She needs to know **when to turn on the heater** so the lab hits a target temperature by a target time.

## The problem

- The lab temperature **drops** at a steady rate when the heater is off.
- Once the heater is turned on, the dropping **stops** and the temp **rises** at a steady rate (~2-2.5 deg F/hr).
- Goal: hit 79-80°F by midnight (both target temp and time are adjustable).

## The math

Given:
- `T_now` = current temperature
- `t_now` = current time
- `r_drop` = cooling rate (°F/hr, positive number)
- `r_rise` = heating rate (°F/hr, positive number, typically 2-2.5)
- `T_target` = desired temperature
- `H` = hours from now until target time

If the heater is turned on `h` hours from now:
- Temp at turn-on: `T_now - r_drop * h`
- Remaining heating time: `H - h`
- Final temp: `(T_now - r_drop * h) + r_rise * (H - h)`

Solving for `h`:

```
h = (T_now + r_rise * H - T_target) / (r_drop + r_rise)
```

The calculator solves this for both `T_target - 0.5` and `T_target + 0.5` to produce a **time window** (e.g. "turn on between 6:28 PM and 6:46 PM"). With the default target of 79.5°F, this gives a 79-80°F range.

## Edge cases

- `h <= 0`: She needs to turn the heater on immediately (or it's already past the window).
- `T_now + r_rise * H < T_target`: Even starting the heater right now won't reach the target. Shows a "too late" warning with the shortfall.

## Design decisions

- **Single HTML file, zero dependencies.** Meant to be opened on a phone browser and bookmarked. Works offline.
- **Dark theme.** Easier on the eyes in a lab setting.
- **Auto-fills current time** on page load so she only has to type the temp and drop rate.
- **Color-coded results:** green = comfortable window, yellow = tight/turn on now, red = too late.
- **Drop stops when heater starts.** The heater fully overcomes cooling — confirmed with Fran. The rise rate is the actual rise, not net-of-cooling.
- **Target temp uses ±0.5°F** around the entered value to give a window rather than a single moment. This matches the real-world tolerance of 79-80°F.

## Verification example

- 70°F at 4:00 PM, dropping 1°/hr, rises 2.25°/hr, target 79.5°F at midnight (8 hrs away)
- `h = (70 + 2.25*8 - 79.5) / (1 + 2.25) = 8.5 / 3.25 ≈ 2.62 hrs` → turn on ~6:37 PM
- At 6:37 PM temp has dropped to 67.4°F. Then 5.38 hrs of heating: `67.4 + 2.25*5.38 = 79.5°F`

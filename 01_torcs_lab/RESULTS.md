# TORCS Lab Results

## Track Used
E-Track (flat circuit — easier for AI tuning)

## Bug Found — Parameters Not Working!

During early testing, I noticed the car was always hitting 160 km/h 
even when TARGET_SPEED was set as low as 10. After investigation, 
the bug was found in torcs_jm_par.py.

The file had TWO `if __name__ == "__main__":` blocks. The first one 
was calling `drive_example()` which had a hardcoded `target_speed=160` 
inside it — completely ignoring all the USER CONFIGURABLE PARAMETERS.

```python
# This was running instead of our code!
def drive_example(c):
    target_speed=160  # hardcoded — ignores TARGET_SPEED completely
```

The fix was to delete the first `if __name__ == "__main__":` block so 
that only `drive_modular()` runs — which actually uses our parameters.

This was a real debugging experience — the code looked correct but 
the wrong function was being called the whole time!

---

## Experiment Results

### Run 1 — Slow & Stable
| Parameter | Value |
|---|---|
| TARGET_SPEED | 50 |
| STEER_GAIN | 40 |
| CENTERING_GAIN | 0.60 |
| BRAKE_THRESHOLD | 0.3 |
| ENABLE_TRACTION_CONTROL | True |

**Lap Time: 8:17:33**
**Observation:** Car was very stable, stayed on track cleanly but was extremely slow.

---

### Run 2 — Medium Speed
| Parameter | Value |
|---|---|
| TARGET_SPEED | 100 |
| STEER_GAIN | 40 |
| CENTERING_GAIN | 0.45 |
| BRAKE_THRESHOLD | 0.5 |
| GEAR_SPEEDS | [0, 20, 40, 80, 100, 110, 180] |
| ENABLE_TRACTION_CONTROL | True |

**Lap Time: 4:18:53**
**Observation:** Good balance between speed and stability. Nearly halved the lap time vs Run 1.

---

### Run 3 — Fast & Aggressive
| Parameter | Value |
|---|---|
| TARGET_SPEED | 160 |
| STEER_GAIN | 25 |
| CENTERING_GAIN | 0.30 |
| BRAKE_THRESHOLD | 0.7 |
| GEAR_SPEEDS | [0, 20, 40, 80, 100, 150, 180] |
| ENABLE_TRACTION_CONTROL | True |

**Lap Time: 2:43:75**
**Observation:** Much faster lap time but car was less stable in corners.

---

## Key Findings

- Doubling TARGET_SPEED from 50 to 100 cut lap time roughly in half
- Higher BRAKE_THRESHOLD means the car brakes later — faster but riskier
- Lower CENTERING_GAIN at high speed means the car drifts more on corners
- STEER_GAIN needs to be lower at higher speeds to avoid oversteering

## One Thing That Surprised Me

The biggest surprise was the hidden bug — no matter how low I set 
TARGET_SPEED, the car always ran at 160 km/h. Finding the root cause 
(two main blocks, wrong function being called).

## One Thing I Would Try Next

Experiment with disabling ENABLE_TRACTION_CONTROL at high speed to see
if it makes the car faster or causes it to spin out completely.

## Summary

This lab showed how a rule-based AI driver responds to parameter tuning.
Small changes to braking, steering and speed targets have a large visible
effect on lap times and stability — just like real racing engineering!
The debugging experience of finding the hardcoded speed was an unexpected
but valuable lesson in reading and understanding code carefully.
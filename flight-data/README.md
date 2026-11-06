# flight-data

Flight logs from the Hyperion-II flight on 2025-08-21, added in `8777a4c`
("upload flight data as submitted to LC"). The two CSVs are the logs as
submitted to Launch Canada, unchanged since that commit.

**This is not the canonical copy.** The complete dataset, and the parsers and
analysis pipeline, live in
[burnoutlabs/post-flight-analysis](https://github.com/burnoutlabs/post-flight-analysis),
under `data/2025-08-21_hyperion2_lc25/`. Use that for any new analysis. This
folder is kept as the record of what was submitted.

## Files

| Path | What it is |
| --- | --- |
| `2025-08-21_primary_blueraven_lr.csv` | Featherweight Blue Raven (primary altimeter), low-rate log, 50 Hz |
| `2025-08-21_redundant_telemega.csv` | Altus Metrum TeleMega (redundant altimeter), AltOS CSV export |
| `fw_br_lr_analysis/` | Event table (with and without thresholds), metadata JSON and plots derived from the Blue Raven log |
| `fw_tm_analysis/` | Event table, metadata JSON and plots derived from the TeleMega log |

Both CSVs are byte-identical to the corresponding raw files in
post-flight-analysis (`raw/src/WERT FBR LR_08-21-2025_14_11_45.csv` and
`raw/src/2025-08-21-serial-6583-flight-0005.csv`; same Git blob hashes).

The contents of the two analysis folders were produced by scripts that are not
in this repository. Treat them as outputs whose generator is not recorded here.

Present in post-flight-analysis and not here: the Blue Raven high-rate log,
the Featherweight GPS tracker log and its KML, the TeleMega `.eeprom` file,
and the Blue Raven deployment settings.

## Airbrakes

The airbrakes listed in the [design summary sheet](../DSS.md) ("Airbrakes -
drogue chute bay") never deployed in flight.

## Flown vs simulated

Times are seconds from launch as each device records it (the TeleMega `time`
column, the Blue Raven `Flight_Time_(s)` column). Heights are above ground
level.

| | Burnout | Apogee time | Apogee height |
| --- | --- | --- | --- |
| TeleMega (state changes; max `height`) | 3.20 s (boost to coast) | 24.05 s (max height); drogue state at 24.10 s | 2854 m |
| Blue Raven LR (flags; max `Baro_Altitude_AGL_(feet)`) | 3.00 s (`Burnout_Coast`) | 24.20 s (max height); `Apogee` flag at 24.36 s | 2852 m (9356.3 ft) |
| Simulation, M2505, `hyperion_II_v3.ork` | 3.0 s (scripted run) | 23.80 s (stored); 23.85 s (scripted run) | 3003 m |
| Simulation, M1290 / M1520, `hyperion_II_v3.ork` | | 25.49 s / 24.89 s | 3055 m / 3058 m |

The design summary sheet lists the M2505 as the chosen reload, with the M1290
and M1520 as alternatives. The simulation rows come from the results stored in
`../ork/hyperion_II_v3.ork` (simulations "LC M2505/M1290/M1520
2024-08-29-0900"). The "scripted run" figures are from
`../ork/outputs-v3/lcProgUpdate1_v3.txt`, which re-runs the first simulation in
that file (the M2505) through orlab.

So the flight matched the simulation closely on timing: burnout within 0.2 s
and apogee within about 0.4 s of the M2505 simulation. Apogee was about 150 m
below the M2505 simulation, and about 200 m below the M1290 and M1520
simulations.

### Timing in post-flight-analysis

Until
[post-flight-analysis#7](https://github.com/burnoutlabs/post-flight-analysis/issues/7)
is fixed, `pfa` derives TeleMega flight time from the GPS clock instead of the
log's own `time` column, so its TeleMega timings for this flight are wrong:
its interim `telemega_std.csv` puts the 2854.23 m peak at 48.35 s rather than
24.05 s. Heights are unaffected. The figures above are read directly from the
raw CSVs in this folder.

## Reproducing the figures

```python
import csv

rows = list(csv.reader(open("2025-08-21_redundant_telemega.csv")))
h = [c.strip() for c in rows[0]]
t, s, z = h.index("time"), h.index("state_name"), h.index("height")
data = [(float(r[t]), r[s].strip(), float(r[z])) for r in rows[1:]]
first = {}
for time, state, _ in data:
    first.setdefault(state, time)
print(first)                          # coast 3.2, drogue 24.1
print(max(data, key=lambda d: d[2]))  # (24.05, ..., 2854.23)

rows = list(csv.reader(open("2025-08-21_primary_blueraven_lr.csv")))
h = rows[0]
ft, agl = h.index("Flight_Time_(s)"), h.index("Baro_Altitude_AGL_(feet)")
burn, apo = h.index("Burnout_Coast"), h.index("Apogee")
print(next(r[ft] for r in rows[1:] if r[burn] == "1"))  # 3.00
print(next(r[ft] for r in rows[1:] if r[apo] == "1"))   # 24.36
peak = max(rows[1:], key=lambda r: float(r[agl]))
print(peak[ft], float(peak[agl]) * 0.3048)              # 24.20, 2851.8
```

# celestrak-mirror

Public read-only cache of selected [CelesTrak](https://celestrak.org) data.
A GitHub Actions workflow re-fetches every 30 minutes from runner IPs that
CelesTrak does not block, so consumers behind blocked datacenter ASNs
(notably Hetzner) can still ingest the data.

## What's mirrored

| Path | Source | Refresh | Format |
|---|---|---|---|
| `tle/<group>.tle` | `celestrak.org/NORAD/elements/gp.php?GROUP=<group>&FORMAT=tle` | 30 min | TLE3 |
| `satcat/satcat.csv` | `celestrak.org/pub/satcat.csv` | 30 min | CSV |
| `LAST_REFRESH` | UTC ISO-8601 of last successful workflow run | 30 min | text |

Groups: `stations`, `starlink`, `oneweb`, `geo`, `weather`, `resource`,
`science`, `military`. Add/remove in `.github/workflows/refresh.yml`.

## Use from a consumer

Read via `raw.githubusercontent.com` — no auth, no token, no rate-limit
beyond GitHub's generous defaults:

```
https://raw.githubusercontent.com/astrion-tech/celestrak-mirror/main/tle/stations.tle
https://raw.githubusercontent.com/astrion-tech/celestrak-mirror/main/satcat/satcat.csv
```

Each file is committed atomically only when the upstream fetch passes a
sanity check (TLE files must have a line count divisible by 3, SATCAT
must be > 1 MB). Stale data from one cycle won't overwrite good data
from the previous cycle.

## Attribution

All data is sourced from CelesTrak, maintained by Dr. T.S. Kelso. Please
respect [CelesTrak's terms of use](https://celestrak.org/contact/),
in particular the rate-limit guidance for upstream consumers.

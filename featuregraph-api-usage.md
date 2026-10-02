# FeatureGraph API — Usage

A hosted endpoint that runs FeatureGraph's deterministic compiler on a known
dataset and returns the resulting objects. No installation required — one
HTTP request in, a JSON table out.

**Current scope:** one dataset (BIDMC respiration), one operation (run the
compiler at a window you choose). This is the open compiler only —
validated, automatic window selection is a separate step we run for you
directly on your own data; see the note at the end.

## Base URL

```
https://featuregraph-api.fly.dev
```

## Authentication

Every request to `/process` requires an API key, sent as a header:

```
X-API-Key: <your key>
```

Requests with a missing or incorrect key are rejected immediately:

```json
{"detail": "Invalid or missing X-API-Key header."}
```

## Health check

```
GET /health
```

No authentication required. Returns `{"status": "ok"}` if the service is up.
Use this to confirm connectivity before debugging anything else.

## Running the compiler

```
GET /process?smooth_window=<integer>
```

**Required header:** `X-API-Key`
**Required query parameter:** `smooth_window` — a positive integer, in
samples, controlling how much the signal is smoothed before the compiler
detects events in it.

### Example request

```bash
curl -H "X-API-Key: YOUR_KEY" \
  "https://featuregraph-api.fly.dev/process?smooth_window=100"
```

### Example response (trimmed — a real call returns one row per detected
object; subject 1 at `smooth_window=100` returns 172)

```json
{
  "dataset": "bidmc",
  "smooth_window": 100,
  "signal_column": "respiration",
  "group_column": "subject",
  "row_count": 172,
  "summary": [
    {
      "subject": 1,
      "respiration_trough_event_id": 1,
      "start_index": 101.0,
      "end_index": 337.0,
      "peak_index": 179.0,
      "trough_index": 101.0,
      "is_complete": true,
      "duration": 236,
      "period": null,
      "rising_duration": 78,
      "falling_duration": 158,
      "amplitude_raw": 0.42962,
      "amplitude_smooth": 0.3483359749999999,
      "temporal_symmetry": 0.6610169491525424,
      "max_raw_signal": 1.0,
      "max_smooth_signal": 0.8792958499999999,
      "min_raw_signal": 0.14076,
      "min_smooth_signal": 0.18262390000000003,
      "raw_rising_mean_rate": 0.011015897435897436,
      "raw_falling_mean_rate": 0.005438227848101266,
      "smooth_rising_mean_rate": 0.008931691666666665,
      "smooth_falling_mean_rate": 0.004409316139240505
    }
  ]
}
```

### Field reference

| Field | Meaning |
|---|---|
| `subject` | Which subject this row belongs to (`group_column` identifies which column this is) |
| `respiration_trough_event_id` | A per-subject counter identifying this object |
| `start_index` / `end_index` | Sample indices bounding the object; `null` for an incomplete leading/trailing object |
| `peak_index` / `trough_index` | Sample index of the object's peak and trough |
| `is_complete` | `false` for a partial object at the very start of a recording (no real boundary on one side) — exclude these from analysis unless you specifically want them |
| `duration` | Total length of the object, in samples |
| `period` | Time since the previous object's trough, in samples (`null` for the first object) |
| `rising_duration` / `falling_duration` | Samples spent rising vs. falling within the object |
| `amplitude_raw` / `amplitude_smooth` | Peak-to-trough amplitude, on the raw signal and on the smoothed signal |
| `temporal_symmetry` | How balanced the rising and falling phases are (0 = all on one side, higher = more symmetric) |
| `max_raw_signal` / `min_raw_signal` / `max_smooth_signal` / `min_smooth_signal` | Signal extremes within this object |
| `raw_rising_mean_rate` / `raw_falling_mean_rate` / `smooth_rising_mean_rate` / `smooth_falling_mean_rate` | Average rate of change during the rising/falling phase, raw and smoothed |

Indices and durations are in samples, not seconds. BIDMC is sampled at
125 Hz, so divide by 125 to convert to seconds if needed.

## Why `smooth_window` matters

This parameter is not cosmetic. Running the identical construction on the
same BIDMC recordings at different windows produces substantially different
object counts — across the full 53-subject cohort, event counts at two
different windows correlated at only 0.39, with per-subject ratios up to
40x. The window you choose is part of the specification of what counts as
an event, not a detail to pick arbitrarily.

## What this endpoint does not do (yet)

- It does not choose `smooth_window` for you. Picking a window that's
  actually appropriate for a given signal, and validating that choice,
  is a separate process — we run it directly on your data as part of
  working with you, rather than exposing it here.
- It only serves one dataset today (BIDMC respiration). Support for
  additional datasets, including the wearable data discussed, is in
  progress.
- It accepts no custom data upload yet. Running this on your own raw
  signals is the next step we're building toward.

## Questions

Reach out directly — this is an early, hand-built service, not a
self-service product yet.

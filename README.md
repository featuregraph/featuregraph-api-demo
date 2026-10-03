# FeatureGraph API Demo

A hosted endpoint that runs FeatureGraph's deterministic compiler on a known
dataset and returns the resulting objects, over a plain HTTP API. 

This repo is a starting point for calling that API: a usage reference and a
runnable notebook. The API itself is a separate, hosted service. 

## Get a key

Contact [Nazia Habib](mailto:nazia.habib@featuregraph.ai) for an API key.

`nori_comparison_demo.ipynb` (see below) also calls Synthefy's hosted Nori
model, which needs its own, separate API key. Get one from Synthefy
directly; it's unrelated to the FeatureGraph key above.

## Quickstart

1. Clone this repo.
2. `pip install -r requirements.txt`
3. Create your own `.env` file with your keys in it:
   ```bash
   cp .env.example .env
   ```
   Then open `.env` and fill in both keys:
   ```
   FEATUREGRAPH_API_KEY=your-featuregraph-key-here
   SYNTHEFY_API_KEY=your-synthefy-key-here
   ```
   (`SYNTHEFY_API_KEY` is only needed for `nori_comparison_demo.ipynb`; the
   starter notebook alone only needs `FEATUREGRAPH_API_KEY`.)
   `.env` is already listed in `.gitignore`, so it's excluded from version
   control automatically — your key never ends up committed or pushed,
   even if you later make changes and push them back to your own fork.
4. Open `featuregraph_api_starter.ipynb` and run it top to bottom.

## What's here

- **`featuregraph-api-usage.md`** — full usage reference: endpoints,
  required headers, the response format, and what every field in the
  result means.
- **`featuregraph_api_starter.ipynb`** — a runnable notebook that calls the
  API, loads the result into a pandas DataFrame, and shows how the
  `smooth_window` parameter changes what gets detected.
- **`nori_comparison_demo.ipynb`** — a small, self-contained comparison
  showing FeatureGraph's objects plugging directly into Synthefy's Nori
  model, alongside a raw-signal baseline and a training-mean control.
  States plainly what it does and doesn't show, and links to our full,
  properly controlled interoperability study for the rigorous version of
  the same question.

## What this API does

Given a smoothing window you choose, it runs FeatureGraph's compiler on a
known signal and returns every detected object (for respiration: a breath)
with its timing, amplitude, and shape. See `featuregraph-api-usage.md` for
the full field reference.

## What this API doesn't do yet

- It doesn't choose the smoothing window for you. That choice measurably
  changes the result (see the usage doc for a concrete number from our own
  published research) -- validated, automatic window selection is a
  separate process we run directly on your data, not exposed here.
- It serves a fixed, known dataset today. Support for bringing your own
  data is in progress.

## Questions

Reach out directly: [nazia.habib@featuregraph.ai](mailto:nazia.habib@featuregraph.ai)

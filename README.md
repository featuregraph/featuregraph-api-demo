# FeatureGraph API — demo

A hosted endpoint that runs FeatureGraph's deterministic compiler on a known
dataset and returns the resulting objects, over a plain HTTP API. No local
installation of FeatureGraph required.

This repo is a starting point for calling that API: a usage reference and a
runnable notebook. The API itself is a separate, hosted service — nothing
in this repo needs to be installed or deployed.

## Get a key

This is an early, hand-built service, not self-service yet. Contact
[Nazia Habib](mailto:nazia.habib@featuregraph.ai) for an API key.

## Quickstart

1. Clone this repo.
2. `pip install -r requirements.txt`
3. Create your own `.env` file with your key in it:
   ```bash
   cp .env.example .env
   ```
   Then open `.env` and fill in the key you were given:
   ```
   FEATUREGRAPH_API_KEY=your-key-here
   ```
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

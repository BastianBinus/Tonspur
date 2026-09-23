# Tonspur

Turns a phone video into text, entirely in the browser. Whisper runs on the device through
[transformers.js](https://github.com/huggingface/transformers.js) 4.3.0. The audio never leaves the
phone. The only download is the speech model from Hugging Face, the first time you use it.

- **Language:** German, English, or both. transformers.js has no Whisper language detection and
  falls back to English without saying so, so `worker.js` decides between `<|de|>` and `<|en|>`
  itself for each 30 s part, inside the same `generate()` call.
- **Cuts:** the audio is split at the quietest 100 ms between 24 s and 30 s, so words don't get cut
  in half. Silent parts are skipped, because Whisper makes up text on silence.
- **Models:** Tiny (≈60 MB), Base (≈135 MB, default), Small (≈250 MB). Runs on the CPU (WASM) by
  default; the GPU (WebGPU) is optional and falls back to the CPU if it fails.

## Running it

Any static HTTPS host works. `worker.js` is loaded as a module worker, so opening `index.html`
straight from disk won't work. Here it's served by GitHub Pages from `main`.

Locally: `python3 -m http.server` in this folder, then open http://localhost:8000.

## Known limits

- Runs on a single thread. GitHub Pages can't send the COOP/COEP headers needed for
  multi-threaded WASM.
- Safari holds the whole video in memory while decoding it, so videos above about 1 GB can make
  the tab reload.
- iOS Safari deletes stored data from sites you haven't opened in 7 days. Adding the page to the
  Home Screen keeps the downloaded model.

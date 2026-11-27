# Cherri Interpreter API

A provider-independent prompt interpretation service deployed as a Cloudflare Worker. The browser page is hosted separately on GitHub Pages.

## Deploy

1. Create a Cloudflare Worker named `cherri-interpreter`.
2. Paste the contents of `worker.js` into the Worker editor (or deploy this folder with Wrangler).
3. Add a Worker secret named `OPENAI_API_KEY` containing an OpenAI API key. Never put this key in the website or GitHub.
4. Optionally set Worker variable `OPENAI_MODEL` (default: `gpt-4.1-mini`).
5. Deploy. Your endpoint will be `https://YOUR-WORKER.YOUR-SUBDOMAIN.workers.dev`.
6. In Interpreter Lab, paste that base URL into the API endpoint setting and save it.

## Routes

- `GET /health` — reports service availability and whether the server secret is configured (never reveals the secret).
- `POST /interpret` — JSON body: `{"prompt":"..." }`; returns the original prompt, structured interpretation, validation and model metadata.

## Security and scope

- API keys are server-side Worker secrets only.
- Prompts are screened with the OpenAI Moderations API before interpretation.
- The interpreter uses Responses API Structured Outputs with a strict JSON Schema, then parses the returned JSON.
- Explicit sexual requests and other flagged categories are blocked rather than rewritten.
- This is prompt interpretation only: it does not upload reference images or call a video-generation provider.
- Add rate limiting and an explicit origin allowlist before opening the endpoint to the public.

API implementation uses the OpenAI Responses API and Structured Outputs. See https://developers.openai.com/api/docs/guides/structured-outputs and https://developers.openai.com/api/reference/resources/responses/methods/create.

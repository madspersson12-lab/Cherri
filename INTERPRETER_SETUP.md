# Cherri Interpreter backend

The Interpreter Lab remains hosted on GitHub Pages. The AI API runs separately as a Vercel serverless function. The browser sends prompts directly to that API; the OpenAI key never reaches the browser.

## Deploy the backend separately

1. Create a Vercel project and import `madspersson12-lab/Cherri`.
2. Set the Vercel project's production branch to `feature/real-interpreter-backend` (or deploy that branch as a preview).
3. In Vercel Project Settings → Environment Variables, add `OPENAI_API_KEY`. Optionally add `CHERRI_INTERPRETER_MODEL` (default: `gpt-4o-mini`).
4. Add `CHERRI_ALLOWED_ORIGINS` with the exact GitHub Pages origin, normally `https://madspersson12-lab.github.io`. For a custom domain, include that origin too. Separate multiple origins with commas.
5. Deploy and note the deployment origin, for example `https://your-cherri-api.vercel.app`.

Do not add the OpenAI key to GitHub, HTML, JavaScript, or GitHub Pages settings.

## Connect GitHub Pages to the API

In the Interpreter Lab page, enter the Vercel deployment origin in the **Deployed interpreter API URL** field. The page saves that address in the current browser only; it does not contain or expose the OpenAI key.

The page then calls `POST https://your-cherri-api.vercel.app/api/interpret`. The API permits only origins listed in `CHERRI_ALLOWED_ORIGINS` (plus localhost for development).

## Test

Health check:

```sh
curl https://YOUR-DEPLOYMENT.vercel.app/api/interpret
```

Interpretation request:

```sh
curl -X POST https://YOUR-DEPLOYMENT.vercel.app/api/interpret \
  -H "Content-Type: application/json" \
  -d '{"prompt":"A red bicycle rolls slowly along a quiet seaside road at sunrise. The camera follows from behind. Keep the same bicycle throughout.","context":{"duration_seconds":8,"camera":"rear tracking shot","motion":"slow, steady movement","continuity":"same red bicycle throughout","visual_style":"cinematic naturalism"}}'
```

A successful response contains provider-neutral structured JSON with the untouched original prompt, interpreted prompt, scene fields, preserved details, and ambiguities.

## Endpoint behavior

- `GET /api/interpret`: health check.
- `POST /api/interpret`: AI interpretation.
- `OPTIONS /api/interpret`: CORS preflight.
- Prompt limit: 6,000 characters.
- Strict JSON Schema output plus server-side validation.
- No video-provider integration, generation call, or persistence.

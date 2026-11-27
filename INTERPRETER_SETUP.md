# Cherri Interpreter backend

The Interpreter Lab uses a same-origin serverless endpoint at `POST /api/interpret`. The core interpreter is provider-neutral: it returns a structured brief and does not call a video-generation service.

## Deploy

This repository is a static HTML site with a Vercel serverless function in `api/interpret.js`.

1. Import the repository into Vercel (or connect the existing Vercel project to this branch).
2. In **Project Settings → Environment Variables**, add `OPENAI_API_KEY` with an OpenAI API key. Keep this value server-side; never add it to HTML or a `NEXT_PUBLIC_*` variable.
3. Optionally set `CHERRI_INTERPRETER_MODEL`. The default is `gpt-4o-mini`, which supports Structured Outputs.
4. Deploy the branch. The static page and `/api/interpret` must be served from the same Vercel deployment.

No npm packages or build step are required. The function calls the OpenAI Responses API directly using the platform's built-in `fetch`.

## Test

Health check:

```sh
curl https://YOUR-DEPLOYMENT.vercel.app/api/interpret
```

Interpretation request:

```sh
curl -X POST https://YOUR-DEPLOYMENT.vercel.app/api/interpret \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "A red bicycle rolls slowly along a quiet seaside road at sunrise. The camera follows from behind. Keep the same bicycle throughout.",
    "context": {
      "duration_seconds": 8,
      "camera": "rear tracking shot",
      "motion": "slow, steady movement",
      "continuity": "same red bicycle throughout",
      "visual_style": "cinematic naturalism"
    }
  }'
```

A successful response contains `interpretation` JSON with the untouched `original_prompt`, a concise `interpreted_prompt`, structured scene fields, explicit preserved details, and ambiguities. The server checks the response before returning it.

## Endpoint behavior

- `GET /api/interpret`: health check.
- `POST /api/interpret`: interpret one prompt.
- Prompt limit: 6,000 characters.
- API key is read only from server environment variables.
- Strict JSON Schema output plus application-level validation.
- No video-provider integration, generation call, or persistence.

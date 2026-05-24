# Custom AI Provider Setup

SVG Animation Studio supports three AI tiers. Pick whichever matches your trust and cost requirements:

| Tier | Who controls the key | Rate-limited? | Best for |
|------|---------------------|---------------|----------|
| **Built-in worker** | Us | Yes (8 req/min) | Trying the app |
| **Your own proxy** | You (server-side) | No | Teams / self-hosters |
| **Direct API key (BYOK)** | You (browser localStorage) | No | Personal use |

---

## Option A — Your Own Proxy Server (Recommended for Teams)

Run your own Cloudflare Worker (or any server). Your API key stays on your server; the browser never sees it. The app sends the same request format our built-in worker receives, so compatibility is guaranteed.

### What the app sends your proxy

```json
POST /
Content-Type: application/json

{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 8192,
  "system": "<system prompt>",
  "messages": [
    { "role": "user", "content": "..." }
  ]
}
```

Your proxy must return an Anthropic-shaped response:

```json
{
  "content": [{ "type": "text", "text": "..." }]
}
```

### Deploy a Cloudflare Worker

This is the same setup the app's built-in worker uses. Copy the code below, deploy it, then paste the worker URL into the app's proxy settings.

**Prerequisites:**
- [Cloudflare account](https://cloudflare.com) (free tier works)
- Node.js 18+
- Wrangler CLI: `npm install -g wrangler`

#### 1. Create the project

```bash
mkdir my-svg-studio-proxy
cd my-svg-studio-proxy
npm init -y
npm install -D wrangler typescript
```

#### 2. `wrangler.toml`

```toml
name = "svg-studio-proxy"
main = "src/index.ts"
compatibility_date = "2024-01-01"
```

#### 3. `src/index.ts`

```typescript
export interface Env {
  ANTHROPIC_API_KEY: string;
  OPENROUTER_API_KEY: string;
  ALLOWED_ORIGIN: string;   // comma-separated, or "*"
  SITE_URL: string;         // optional — shown in OpenRouter logs
  SITE_NAME: string;        // optional — shown in OpenRouter logs
}

const ANTHROPIC_URL  = 'https://api.anthropic.com/v1/messages';
const OPENROUTER_URL = 'https://openrouter.ai/api/v1/chat/completions';

// Model naming convention:
//   "claude-sonnet-4-20250514"           → Anthropic direct
//   "anthropic/claude-sonnet-4-20250514" → OpenRouter
//   "openai/gpt-4o"                      → OpenRouter
function usesOpenRouter(model: string): boolean {
  return model.includes('/');
}

interface AnthropicRequestBody {
  model: string;
  max_tokens?: number;
  system?: string;
  messages: { role: string; content: string }[];
  [key: string]: unknown;
}

function toOpenRouterBody(parsed: AnthropicRequestBody): string {
  const messages: { role: string; content: string }[] = [];
  if (parsed.system) messages.push({ role: 'system', content: parsed.system });
  messages.push(...parsed.messages);
  return JSON.stringify({
    model: parsed.model,
    max_tokens: parsed.max_tokens,
    messages,
  });
}

async function normaliseOpenRouterResponse(res: Response): Promise<Response> {
  if (!res.ok) return res;
  const json = await res.json() as {
    choices?: { message?: { content?: string } }[];
  };
  const text = json.choices?.[0]?.message?.content ?? '';
  return new Response(JSON.stringify({ content: [{ type: 'text', text }] }), {
    status: 200,
    headers: { 'content-type': 'application/json' },
  });
}

function originAllowed(origin: string | null, allowed: string): boolean {
  if (!origin) return false;
  if (allowed === '*') return true;
  return allowed.split(',').map(s => s.trim()).includes(origin);
}

function corsHeaders(origin: string): Record<string, string> {
  return {
    'access-control-allow-origin': origin,
    'access-control-allow-methods': 'POST, OPTIONS',
    'access-control-allow-headers': 'content-type',
  };
}

export default {
  async fetch(req: Request, env: Env): Promise<Response> {
    const origin = req.headers.get('origin');

    if (req.method === 'OPTIONS') {
      if (!originAllowed(origin, env.ALLOWED_ORIGIN)) {
        return new Response('Forbidden', { status: 403 });
      }
      return new Response(null, { status: 204, headers: corsHeaders(origin!) });
    }

    if (req.method !== 'POST') {
      return new Response('Method Not Allowed', { status: 405 });
    }

    if (!originAllowed(origin, env.ALLOWED_ORIGIN)) {
      return new Response('Forbidden', { status: 403 });
    }

    const contentLength = parseInt(req.headers.get('content-length') ?? '0', 10);
    if (contentLength > 1_048_576) {
      return new Response('Payload Too Large', { status: 413 });
    }

    let parsed: AnthropicRequestBody;
    try {
      parsed = JSON.parse(await req.text()) as AnthropicRequestBody;
    } catch {
      return new Response('Bad Request: invalid JSON body', { status: 400 });
    }

    if (!parsed.model || typeof parsed.model !== 'string') {
      return new Response('Bad Request: missing model field', { status: 400 });
    }

    let upstream: Response;

    if (usesOpenRouter(parsed.model)) {
      if (!env.OPENROUTER_API_KEY) {
        return new Response('Worker misconfigured: OPENROUTER_API_KEY not set', { status: 500 });
      }
      upstream = await fetch(OPENROUTER_URL, {
        method: 'POST',
        headers: {
          'content-type': 'application/json',
          'authorization': `Bearer ${env.OPENROUTER_API_KEY}`,
          ...(env.SITE_URL  ? { 'HTTP-Referer': env.SITE_URL  } : {}),
          ...(env.SITE_NAME ? { 'X-Title':       env.SITE_NAME } : {}),
        },
        body: toOpenRouterBody({ ...parsed, max_tokens: Math.min(parsed.max_tokens ?? 8192, 8192) }),
      });
      upstream = await normaliseOpenRouterResponse(upstream);
    } else {
      if (!env.ANTHROPIC_API_KEY) {
        return new Response('Worker misconfigured: ANTHROPIC_API_KEY not set', { status: 500 });
      }
      const body: Record<string, unknown> = {
        model:      parsed.model,
        max_tokens: Math.min(parsed.max_tokens ?? 8192, 8192),
      };
      if (parsed.system)   body.system   = parsed.system;
      if (Array.isArray(parsed.messages)) body.messages = parsed.messages;

      upstream = await fetch(ANTHROPIC_URL, {
        method: 'POST',
        headers: {
          'content-type':      'application/json',
          'x-api-key':         env.ANTHROPIC_API_KEY,
          'anthropic-version': '2023-06-01',
        },
        body: JSON.stringify(body),
      });
    }

    return new Response(await upstream.text(), {
      status: upstream.status,
      headers: { 'content-type': 'application/json', ...corsHeaders(origin!) },
    });
  },
};
```

#### 4. Deploy and configure secrets

```bash
# Deploy the worker
wrangler deploy

# Set your Anthropic key (for plain model IDs like "claude-sonnet-4-20250514")
wrangler secret put ANTHROPIC_API_KEY

# Set your OpenRouter key (for namespaced IDs like "anthropic/..." "openai/...")
wrangler secret put OPENROUTER_API_KEY

# Allow requests from your deployed app (or "*" to allow all origins)
wrangler secret put ALLOWED_ORIGIN
# e.g. https://my-studio.pages.dev

# Optional — shows your app name in OpenRouter logs/rankings
wrangler secret put SITE_URL
wrangler secret put SITE_NAME
```

Your worker URL will look like:
```
https://svg-studio-proxy.<your-subdomain>.workers.dev
```

#### 5. Configure the app

1. Open the editor → click the **⚙** gear icon in the AI chat panel
2. Select **"Your proxy server"** tab
3. Paste your worker URL (e.g. `https://svg-studio-proxy.yourname.workers.dev`)
4. Optionally set a model override (leave blank to use your worker's default)
5. Save — you'll see a **🔒 Own Proxy** badge in the chat header

#### Local development

Run `wrangler dev` inside your project directory. It binds to `http://localhost:8787` by default — paste that as your proxy URL while developing locally.

---

## Option B — Direct API Key (BYOK)

Connect any OpenAI-compatible provider directly from your browser. The key is stored only in your browser's localStorage and is never sent through our servers.

**Supported providers:**

| Provider | Base URL | Example model |
|----------|----------|---------------|
| OpenAI | `https://api.openai.com/v1` | `gpt-4o` |
| OpenRouter | `https://openrouter.ai/api/v1` | `anthropic/claude-sonnet-4-20250514` |
| Mistral | `https://api.mistral.ai/v1` | `mistral-large-latest` |
| Ollama (local) | `http://localhost:11434/v1` | `llama3.2` |
| Any OpenAI-compatible endpoint | your URL | your model |

**Setup:**
1. Open the editor → click **⚙** in the AI chat panel
2. Select the **"Direct API key"** tab
3. Choose a preset or enter a Base URL manually
4. Enter your API key
5. Enter the model name
6. Save — you'll see an **⚡ Custom AI** badge in the chat header

> **Security note:** Your key is stored in `localStorage` and sent directly to the provider in an `Authorization: Bearer` header. It is never transmitted to our worker. Clear it any time via Settings → "Remove custom provider".

---

## Switching back to the built-in worker

Open the AI provider settings and click **"Remove custom provider"**. Rate limits re-apply (8 requests per minute per browser).

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| `403 Forbidden` from your proxy | `ALLOWED_ORIGIN` doesn't include your app's origin | Add your app URL to the secret, or set `*` |
| `500 Worker misconfigured` | API key secret not set | Run `wrangler secret put ANTHROPIC_API_KEY` |
| `Provider error 401` (BYOK) | Wrong or expired API key | Re-enter your key in settings |
| `Provider error 404` (BYOK) | Wrong Base URL | Check provider docs — make sure URL ends with `/v1` |
| Response parses as empty | Model returned non-JSON | Switch to Edit mode; check the model supports instruction-following |

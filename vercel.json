// Vercel serverless function: /api/trace
// Proxies vision requests to the Anthropic API using your secret key.
// The browser calls /api/trace — the key never leaves the server.

// Vision-capable model. Change this if your key uses a different one.
const MODEL = 'claude-sonnet-4-6';

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  const apiKey = process.env.ANTHROPIC_API_KEY;
  if (!apiKey) {
    return res.status(500).json({ error: 'ANTHROPIC_API_KEY env var not set in Vercel' });
  }

  try {
    // req.body is auto-parsed JSON: { messages, max_tokens }
    const body = typeof req.body === 'string' ? JSON.parse(req.body) : (req.body || {});

    const upstream = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': apiKey,
        'anthropic-version': '2023-06-01'
      },
      body: JSON.stringify({
        model: MODEL,
        max_tokens: body.max_tokens || 1024,
        messages: body.messages || []
      })
    });

    const data = await upstream.json();
    return res.status(upstream.status).json(data);
  } catch (err) {
    return res.status(500).json({ error: String(err && err.message || err) });
  }
}

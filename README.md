# paper-digest — an OpenClaw skill

📚 Recent arXiv papers **semantically ranked to your research interests**, each
with a relevance score and a one-line TL;DR. Powered by the hosted
[paper-digest.ericspencer.us](https://paper-digest.ericspencer.us) service — no
API key.

Unlike a raw arXiv keyword search, this ranks by natural-language interests and
returns a plain-language summary per paper, so an agent can answer "what's new
and relevant in my field this week?" in a single call.

## Install

```bash
picoclaw skills install EricSpencer00/openclaw-skill-paper-digest
# or on ClawHub:
picoclaw skills install --registry clawhub paper-digest
```

## Use

```bash
curl -s "https://paper-digest.ericspencer.us/api/papers?interests=large+language+model+agents,reinforcement+learning&days=7&max=5" \
  | jq -r '.papers[] | "• \(.title)  (\((.score*100)|round)% match)\n  \(.tldr)"'
```

See [`SKILL.md`](./SKILL.md) for parameters, response shape, and tips.

## Notes

- Up to **5** interests per query (the service rejects more).
- Papers/metadata come from arXiv; this service is not endorsed by arXiv and
  uses its open-access interoperability.

MIT licensed.

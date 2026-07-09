---
name: paper-digest
description: Semantically-ranked recent arXiv papers by research interest, with TL;DRs.
homepage: https://paper-digest.ericspencer.us
metadata: {"nanobot":{"emoji":"📚","requires":{"bins":["curl"]}}}
---

# Paper Digest

Recent arXiv papers **ranked to your research interests** by a hosted semantic
service — not keyword search. Each result comes back with a relevance score and
a one-line TL;DR, so you can surface "what's new and relevant this week" in one
call. No API key.

Service: <https://paper-digest.ericspencer.us> · Endpoint: `GET /api/papers`

## Quick use

Pass free-text interests (natural language works better than single keywords):

```bash
curl -s "https://paper-digest.ericspencer.us/api/papers?interests=large+language+model+agents,reinforcement+learning&days=7&max=5"
```

Returns JSON: `{ query, ranking:"semantic", generated_at, papers:[...] }`.

Pretty one-line-per-paper with `jq`:

```bash
curl -s "https://paper-digest.ericspencer.us/api/papers?interests=LLM+formal+verification,theorem+proving&days=7&max=5" \
  | jq -r '.papers[] | "• \(.title)  (\((.score*100)|round)% match)\n  \(.tldr)\n  \(.abs_url)"'
```

## Parameters

| Param | Meaning | Notes |
| --- | --- | --- |
| `interests` | comma-separated free-text topics | **max 5** — the service 400s on more |
| `days` | look-back window | default 7 |
| `max` | max papers returned | default 10 |
| `min_score` | relevance floor (0–1) | default 0.62 |
| `categories` | optional arXiv category filter (e.g. `cs.LO,cs.AI`) | |

URL-encode spaces as `+` or `%20`. Commas separate interests.

## Response shape

Each item in `papers[]`:

```json
{
  "id": "2607.06341",
  "title": "Harnessing Code Agents for Automatic Software Verification",
  "abs_url": "https://arxiv.org/abs/2607.06341v1",
  "pdf_url": "https://arxiv.org/pdf/2607.06341v1",
  "authors": ["..."],
  "primary_category": "cs.SE",
  "published": "2026-07-05T...Z",
  "score": 0.72,
  "tldr": "One-sentence plain-language summary of the paper.",
  "relevance_blurb": "Why this matches your stated interests.",
  "abstract": "Full abstract text."
}
```

If nothing clears `min_score`, `papers` is `[]` and a `note` explains how many
were considered — surface that as "no strongly-relevant papers this week"
rather than inventing results.

## Tips

- **Keep it to your top 5 interests.** More than 5 is rejected; if the caller
  gives more, use the 5 most important.
- `score` is 0–1; multiply by 100 for a "% match" badge.
- Prefer `tldr` for a compact digest line; fall back to `relevance_blurb` when
  `tldr` is absent.
- Widen `days` (e.g. 14) when a narrow interest returns nothing.
- This is a read-only public service; degrade loudly on a non-200 (say the
  service was unavailable) instead of fabricating papers.

## Attribution

Papers and metadata originate from arXiv. Per arXiv's terms, this service is not
reviewed or endorsed by arXiv; it uses arXiv's open-access interoperability.

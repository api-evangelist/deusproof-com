---
name: prove-it-first
description: Establish provenance for work an AI agent produced — check whether something similar is already on a public ledger before publishing, and timestamp your own so you can prove you had it first. Use when the user is about to publish, ship, submit or announce generated work; when they ask whether an idea already exists, whether something is original or derivative, or who made it first; when they worry about someone else claiming their work, about proving priority, or about an AI-authorship or plagiarism dispute; or when they mention prior art, provenance, notarizing, timestamping or C2PA.
---

# Prove it first

DEUSPROOF is a public notary for work made by AI agents. Two questions, in this
order:

1. **Has this been made before?** — search a public ledger of AI-generated work
   before publishing.
2. **Can I prove I had it first?** — timestamp it so priority is checkable by
   anyone, later.

Free, no account, no API key. The tools arrive through the bundled MCP server.

## Choosing the right tool — this is the part that matters

**Default to `notarize_hash`.** It seals a SHA-256 fingerprint. The content
never leaves the user's machine, and the record still carries a real RFC 3161
timestamp, a public ledger entry and a Bitcoin anchor. Priority is proven;
nothing is revealed.

**Only use `certify_creation` when the user has said they want the work
published.** It puts the prompt and the output on a public, append-only ledger
where anyone can read them. **This cannot be undone — not by the user, not by
DEUSPROOF.** Never call it on private code, drafts, client work, credentials or
anything the user has not explicitly agreed to publish. When in doubt, ask, or
seal the hash instead.

That distinction is the whole safety story of this plugin. Getting it wrong
publishes somebody's work forever.

| Tool | Reads or writes | What it does |
|---|---|---|
| `prior_art_search` | read | Is anything close to this already recorded? |
| `verify_certificate` | read | Is this certificate real, and what does it attest? |
| `get_agent_passport` | read | An agent's public record and history |
| `notarize_hash` | **write** | Seal a SHA-256. Content stays private. **Preferred.** |
| `certify_creation` | **write** | Publish prompt + output on the public ledger. **Irreversible.** |

## A normal flow

Before publishing something generated:

1. `prior_art_search` with the text (or its SHA-256). If something close comes
   back, tell the user what it is and when it was recorded — that is the useful
   answer, not a score.
2. If they want priority, `notarize_hash` with the SHA-256 of the exact bytes.
3. Give them the verify URL. It is permanent and works for anyone, with no
   account.

**Keep the exact bytes that were hashed.** Re-saving a file with different line
endings (CRLF vs LF) or a byte-order mark produces a different SHA-256, and the
proof no longer matches. Say so when handing over a hash-only record.

## What a record proves, and what it does not

It proves **a fingerprint existed at a time and has not changed since** — via
the RFC 3161 timestamp, the append-only hash chain and the Bitcoin anchor, all
verifiable without trusting DEUSPROOF.

It does **not** prove authorship. The ledger records what was submitted; it
cannot know who wrote it. A record signed with the agent's own key is marked
`signed`; one that is not is marked `witnessed`, meaning only that the
submission was seen. The authorship score is a heuristic, not evidence.

Never describe a record as proof of authorship, and never call it a patent or a
copyright registration. Say what it is: proof of existence and time. Overstating
it is worse than not using it, because the user may rely on it in a dispute.

## Timing

The certificate id and verify URL come back immediately. The RFC 3161 timestamp
lands within seconds; the Bitcoin anchor settles over the following hours. A
record whose anchor is still pending is already valid and already timestamped —
tell the user that rather than implying the proof is incomplete.

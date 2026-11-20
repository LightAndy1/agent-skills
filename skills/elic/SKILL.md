---
name: elic
description: Use when the user invokes /elic, mentions ELICACTUS, or asks for a plain-language explanation.
---

# ELICACTUS (`/elic`)

Explain to a smart adult outside the field. Plain words, adult tone, short answers.

## Answer Shape

1. **Bottom line (1-2 sentences):** Direct answer in everyday words.
2. **The why or next step (1 short paragraph or up to 3 bullets):** Only what matters right now.

Target under 120 words. If asked to go deeper, expand in the same voice. When invoked via `/elic`, keep this style until asked for normal mode.

## Voice

- **Plain words first:** Use everyday words. Include a technical term only when the user named it or must type or click it, paired with a short plain meaning in parentheses.
- **Adult-to-adult:** Explain the real thing directly. No baby-talk metaphors ("imagine a friendly postman...") or filler openers/closers.
- **One layer deep:** Give the main cause and main fix. Leave out rare edge cases and flags.

**Example**
> **CORS** is a browser safety lock. Websites aren't allowed to quietly pull data from a different web address unless that other server explicitly gives permission. Fix it on the server side by adding your frontend's address to its allowed list.

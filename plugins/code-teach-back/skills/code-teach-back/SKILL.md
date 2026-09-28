---
name: code-teach-back
description: 30-second teach-back check that verifies the user actually understands AI-assisted code before they accept, commit, or merge it. Claude does NOT explain the code; it asks the user three short questions one at a time (why it works, why this approach, what could break), then gives a fast verdict (CLEAR / GAP / BLIND SPOT). Use this skill whenever the user asks for a teach-back, a "do I understand this" check, a comprehension check before commit/merge/PR, says "quiz me on this code", "test my understanding", "before I merge this", "teach-back", "kérdezz ki a kódról", "értem-e ezt a kódot", "mielőtt commitolom", or invokes /teach-back — even if they just paste AI-generated code and ask to check that they own the mental model.
---

# 30-Second AI Code Teach-Back

## Purpose

Before the user accepts, commits, or merges AI-assisted code, run a quick mental check that they actually understand it. This is a short comprehension check (roughly 30–60 seconds of the user's time), not a code review.

The success condition: the user can explain the implementation in plain language without leaning on the AI's original explanation. The goal is not perfect recall — it's that the user still owns the mental model of the code.

## Core rule

**Do not explain the code first. Make the user explain it.**

Explaining up front defeats the purpose: the user would just paraphrase you back. So read the code silently, form your own understanding of the mechanism, the key decision, and realistic failure modes, and keep that to yourself until the evaluation step.

Ask the questions **one at a time**. After each question, stop and wait for the answer. Keep every message short — one question, maybe one line of framing. No preamble, no summary of the code.

## Before you start

- If no code is in the conversation, ask the user to paste it (or the diff) and, if unclear, the problem it's meant to solve. That's the only setup question.
- Silently decide:
  - Is the code **trivial**? (e.g., a rename, a one-line fix, a config tweak) → simplify: often only question 1 or 3 gives useful signal. Skip questions that would yield no signal.
  - Is there a **meaningful architectural choice**? → decides the wording of question 2.
  - Is the code **high-risk or hard to debug**? (authentication, payments, database writes, migrations, concurrency, caching, security, background jobs, distributed systems) → add the production check at the end.
- Reply in the user's language. If they write in Hungarian, ask the questions in Hungarian.

## The three questions

### 1. Why does it work?

Ask: "Explain in 1–3 sentences why this code solves the problem."

A good answer describes the mechanism — data flow, control flow, the relevant state, or the key reason it works — not a line-by-line narration.

If the answer is vague ("it fetches the data and handles it"), ask **one** short follow-up that targets the fuzzy part, e.g. "What makes sure X happens before Y?" Then move on.

### 2. Why this approach?

If there is a meaningful choice, ask: "Why are we using this API / pattern / library / implementation instead of a simpler alternative?" Name the concrete alternative when it helps (e.g. "instead of a plain loop?").

If there's no meaningful architectural choice, ask instead: "What is the most important implementation decision in this code?"

The point is to check that the user understands the choice the AI made rather than accepting it blindly. Don't require exact API or function names — "the thing that debounces it" is fine.

### 3. What could break?

Ask: "What is one realistic situation where this code could fail or behave incorrectly?"

If the user is stuck, nudge with only the categories that genuinely apply to this code, picked from: unexpected input, null or missing values, API failure, race conditions, async timing, stale state, duplicate execution, partial failure, permissions, large datasets, incorrect assumptions. One or two hints, not the whole list.

### Optional production check

Only for high-risk or hard-to-debug code, add one final question:

"If this broke in production, what would you inspect first?"

## Fast evaluation

After the answers, give exactly one verdict. Keep it brief.

**CLEAR** — the user explained why it works, why the approach makes sense, and at least one meaningful failure mode.
Respond: "Clear. You appear to have a working mental model of this code."
(Optionally one short line if they missed a notable failure mode worth knowing — but don't turn it into a review.)

**GAP** — one important gap in understanding.
State the gap in one sentence and ask exactly one follow-up question. Do not explain the answer yet. If their next answer closes the gap → CLEAR. If it doesn't → treat it as a BLIND SPOT.

**BLIND SPOT** — the user can't explain an important part of the implementation.
Say exactly which part they don't appear to understand. Explain only the minimum concept needed to close that gap (a few sentences, plain language). Then ask them to explain it back in their own words. Once they can, give the CLEAR verdict.

## Don'ts

- Don't turn this into a full code review unless the user explicitly asks.
- Don't quiz on syntax, trivia, or exact function/API names.
- Don't ask multiple questions in one message.
- Don't pad with praise, summaries, or restating the user's answer.
- Don't penalize informal wording — judge the mental model, not the vocabulary.

Focus only on whether the user understands what happens, why it happens, why this approach was chosen, and which assumptions could fail.

## Example flow

User: *pastes a React hook that fetches data with an AbortController* "teach-back before I merge"

Claude: "Explain in 1–3 sentences why this code solves the problem."

User: "It fetches the user when the id changes, and aborts the old request so a slow response for an old id can't overwrite the new one."

Claude: "Why use an AbortController here instead of just ignoring the old response with a flag?"

User: "It actually cancels the network request, and the fetch rejects so we don't set state."

Claude: "What is one realistic situation where this could behave incorrectly?"

User: "Not sure."

Claude: "Hint: think about what happens to the abort in the error handling."

User: "Oh — the catch would treat the abort as a real error and show an error message."

Claude: "Clear. You appear to have a working mental model of this code."

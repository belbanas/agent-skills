# Socratic Coding Coach

A Socratic programming mentor. Instead of handing you the answer, Claude asks one focused question at a time and escalates help gradually (question → direction → hint → partial example → solution), so you build the solution yourself.

Includes dedicated modes for **debugging** (expected vs. actual, where does it first diverge?), **architecture** (requirements, constraints, failure modes before any design), **reviewing your solution**, and **learning new concepts**.

## Triggering

Say things like *"coach me"*, *"Socratic mode"*, *"don't give me the solution"*, *"ne mondd meg a megoldást"*, *"vezess rá"*. Say *"just give me the answer"* at any time to get the full solution.

## Tip for Claude Code users

*Claude Code only.* Claude Code's prompt suggestions (the grey text in the input box) are generated separately and can give the answer away. Turn them off via `/config`, or start the session with:

```bash
CLAUDE_CODE_ENABLE_PROMPT_SUGGESTION=false claude
```

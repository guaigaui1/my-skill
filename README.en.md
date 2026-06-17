# fable-learning

*[中文版 / Chinese version](./README.md)*

A Claude Code Skill. It teaches a concept, design idea, or piece of knowledge **indirectly through a fable** — letting the reader live through the mechanism's cause-and-effect and feel an "aha" *before* it is ever named, then adding a short reveal that maps the story back to the concept.

The skill body lives in [`SKILL.md`](./SKILL.md) (Chinese-default). An English-default variant is at [`en/SKILL.md`](./en/SKILL.md) (skill name `fable-learning-en`).

## The two variants

This repo ships two skills with identical instructions that differ only in default language. Install whichever fits:

| | `fable-learning` | `fable-learning-en` |
|---|---|---|
| File | [`SKILL.md`](./SKILL.md) | [`en/SKILL.md`](./en/SKILL.md) |
| Default fable language | Chinese | English |
| How it writes | Identical: find the kernel → fable body (no jargon) → reveal, separated by `---` | Same |
| Best for | Chinese-native readers | English output / English readers |

The names differ, so they don't collide and can be installed together. Either way, you can switch language on the fly in one sentence (e.g. tell the Chinese-default skill "do it in English").

## Install

Drop the directory (with `SKILL.md`) where Claude Code discovers skills:

```
~/.claude/skills/fable-learning/SKILL.md       # personal / global
# or
<project>/.claude/skills/fable-learning/SKILL.md  # project-level
```

Once placed, it is auto-discovered in any new session.

> Note: `SKILL.md` defaults to writing the fable in **Chinese** unless you ask for another language. To get an English fable, just say so (e.g. "explain it with a fable, in English").

## How to use

Trigger it with plain language, for example:

- "Explain idempotency to me with a fable, in English."
- "What's the difference between idempotency, retry, and compensation? Use an analogy."
- "I don't get why I should write unit tests — tell me a story so it clicks."

Claude follows the three moves in `SKILL.md`: **find the kernel → tell the fable (no jargon) → reveal, separated by `---`**.

## Example

Here is a complete example written with this skill. The concept is **idempotency**, and the single kernel is: **doing the same operation once or many times leaves the system in the same state, so retrying is safe.**

---

Old Zhou waits for the elevator.

He presses the button. It lights up. Two seconds pass, nothing moves, so he presses it again. Still nothing. Annoyed, he jabs it seven, eight times, as if trying to push the button through the wall.

A kid beside him watches, then joins in, poking it even more eagerly.

*Ding.* The doors open.

One elevator comes down. Not seven, not a dozen. Old Zhou pressed ten times, the kid pressed twenty, and what arrives is this single car, no more, no less. As they squeeze in, Old Zhou finds his frantic jabbing a little funny — **pressing once and pressing thirty times are, to the elevator, the same act**: you ask it to come, it makes one trip, no matter how many times you shout.

He remembers yesterday's online order. The page froze, so he clicked "Place order" again — and two identical packages showed up at his door. That button, it turns out, was nowhere near as forgiving as this elevator button.

---

That elevator button was **idempotent**.

Idempotency means: **an operation performed once and performed N times leaves the system in exactly the same final state.** "Summon the elevator" is idempotent — press it however many times, one trip comes. The "Place order" button is not: click twice, buy twice. That was Old Zhou's trouble.

Mapping the story back: **the frantic jabbing = client retries** (network timeouts, impatient users); **only one elevator arriving = duplicate requests collapsing into a single result**; **the second package = side effects produced by a non-idempotent endpoint under retry**.

Why it's designed this way: in distributed systems, timeouts, dropped packets, and resends are the norm — you **cannot prevent** retries from happening. So instead of praying that callers click only once, you make the operation "the same no matter how many clicks" — turning retries from a hazard into free insurance. Reads and queries are naturally idempotent; side-effecting operations like "create order" or "transfer money" must be *made* idempotent with unique request IDs and dedup tables — engineered to behave like that elevator button.

Where to use it: any write that might be retried (payments, orders, message consumption). Where not to: operations that are *meant* to accumulate — "top up my account by $10" should add once per press, and here idempotency is exactly what you don't want.

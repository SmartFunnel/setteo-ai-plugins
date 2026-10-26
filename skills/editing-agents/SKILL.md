---
name: editing-agents
description: How to create, fix and maintain Setteo setter agents and personas through the Setteo MCP without degrading them. Use this skill whenever the user works on a Setteo agent, persona, script, step, objection or agent context, and above all when they report a bad conversation or a complaint about the agent and ask for a fix ("l'agent a dit X, corrige", "il répète", "il est trop long", "il est trop froid", "rends-le plus chaleureux", "ajoute une règle", or relayed for someone else: "le client trouve que…"), change the agent's tone, prepare an agent for a new campaign or edition, duplicate an agent, or clean up a long persona. Use it even for a one-line change: small patches are where agents drift.
---

# Editing Setteo agents

A Setteo agent answers leads on WhatsApp or Instagram. What you write through the MCP becomes
its prompt, so every sentence you add is read by the model **on every reply**, for every lead.
Agents rarely break in one big edit. They drift through dozens of small patches, each one
reasonable on its own, until the prompt is mostly repetition, history and rules that fight each
other. This skill is about keeping each edit from adding to that.

## How the agent reads what you write

On every reply the model receives, in this order:

1. The platform's own rules (hidden, not editable).
2. The **persona** and the agent **context**.
3. The **whole script**: every step, every objection, every edge condition. Not just the step
   the lead is on.

Precedence is persona > step > platform. Two consequences drive everything below:

- A rule copied into 20 steps is not "a reminder at the right moment". The model reads it 20
  times in the same prompt, and it drowns what each step actually has to say.
- When two places say different things, the model picks one, and not always the same one.
  Adding a stronger rule next to a weaker one creates a contradiction, it does not settle it.

Read `app://resources/agent-platform-rules` once per session before writing a persona, a
context or a step. It is the reference this skill builds on: where each kind of rule belongs,
what the platform already governs (rephrasing, message shape, links, AI disclosure, opt-out,
confidentiality…), and the short rules for changing an existing agent. Restating what the
platform governs costs tokens on every message and changes nothing. The sections below are the
working method around those rules.

## Where things go

| Content | Place |
|---|---|
| Identity, voice, tu/vous, length and question budgets, emoji, banned words, what is never promised | **Persona**, once |
| The offer, prices, dates, programme, speakers, proofs, audience, links | **Agent context** (links as script variables) |
| What to say or obtain at this step, plus at most one local constraint | **Step instruction**, a few sentences, never above 2,000 characters |
| When to go from one step to another | **Edge condition**, one line naming the signal |
| Punctuation stripping, delays, opening hours | **Agent settings**, not prompt text |

If something does not fit any row (a rule for every tenant, a behaviour the platform gets
wrong), it is a platform change: tell the user instead of working around it in the prompt.

## Applying feedback: the fix loop

Most requests look like "the agent did X, fix it", whether the user saw it themselves or relays
someone they run the agent for. Follow these steps in order;
skipping step 2 is how agents end up with the same rule in four places.

1. **Understand the failure.** Read the conversation (`view-conversation`) or reproduce it in a
   sandbox. Name the behaviour in one sentence: what it did, what it should have done.
2. **Find where that behaviour is governed today.** `view-agent` returns the persona text, the
   context and the script; use `search` with a phrase from the faulty message or the rule, and
   `summary: true` to map a long script. Look in the persona, the context, the steps, the edges
   and the settings. Usually the rule already exists somewhere, sometimes twice.
3. **Change it where it lives.** Rewrite that rule so it covers the case. If it exists in
   several places, keep one and delete the others. If it contradicts another rule, settle which
   one wins and remove the loser. Only add a new rule when nothing governs the behaviour yet,
   and add it in the place the table above gives.
4. **Show the user the result before writing.** Show the final text of every rule you touch,
   and what you remove. Use `dry_run: true` on `update-agent-nodes` to show the exact change.
   The user approves the text the model will read, not your summary of it.
5. **Check it.** Replay the case in the sandbox, then run `review-agent` on the agent and
   report what it finds. Fix what your change caused; list the rest for the user.

A fix that only adds text is a warning sign. A good fix often leaves the agent shorter.

**When the user asks for exactly what this skill advises against** (a « règle prioritaire » in
every step, a copy of the agent, the ticket number in the prompt), say once, plainly, why it
would make the agent worse and what you would do instead. Then the user decides: it is their
agent. If they still want it, do it as cleanly as possible, without arguing again.

## Never write into an agent

The model cannot use any of these, and each one is read on every reply:

- **History**: dates of fixes, ticket numbers, who asked for the rule, "ajouté le 07/09",
  "imposé par la cliente", "après un cas réel". Put the history in your answer to the user or
  in their ticket, not in the prompt.
- **Transcripts and rejected outputs**: pasted conversations, ❌ lists of what the agent said
  wrong. Describe the expected behaviour instead. Good examples (✅) belong in the persona's
  examples, not scattered through steps.
- **Escalation**: `⚠⚠`, `🔴 PRIORITAIRE`, `RÈGLE N°1`, "elle prime sur le persona", capitals
  asserting importance. If a rule is not followed, it is unclear, contradicted or in the wrong
  place: fix that. Louder versions of the same rule do not work, and the agents that tried
  say so themselves.
- **Notes for humans**: "cette instruction est là pour la lecture humaine", work markers like
  `[+GF]`, `NEW_` prefixes left in ids, `A_COMPLETER` or `[lien Calendly]` placeholders.
- **Instructions on a verbatim step**: when a step sends its message as-is (`send_exact`), its
  instruction is never given to the model. Keep it empty or one line.

## Campaigns and versions

- **One agent per funnel, updated per edition.** Change the dates and offer in the context
  for the next edition instead of cloning the agent. Clones multiply every later fix and drift
  apart (different dates, different rules) within weeks.
- **Dates live in the context**, never in steps, so one edit updates the whole agent. The one
  exception is a message sent verbatim, which cannot read the context: it keeps its date, and
  must be updated with the context.
- **Check every date against the calendar before writing it, with a command, not from
  memory.** Leads read « lundi 17 » in the message, and if the 17th is a Tuesday the agent is
  wrong for everyone. Weekdays recalled from memory are often wrong, so compute them, for
  example `python3 -c "import datetime; print(datetime.date(2026, 11, 17).strftime('%A'))"` or
  `date -j -f %Y-%m-%d 2026-11-17 +%A` on macOS. Do the same for dates you derive yourself,
  such as « the Sunday after the event ». When the user's dates and weekdays don't match the
  year, ask which one is right; don't pick.
- **When an event is over, tell the user the agent should be switched off** (activation is
  done in the studio). Agents left active after their event invite leads to past dates.
- **Links are variables**: declare them with plain names (`lien_rdv`, never `{{lien_rdv}}` or
  accented names) and write `{{lien_rdv}}` in the text. Never leave a variable with a
  placeholder value.

## Personas

- A persona says who writes and how. The offer, campaign facts and objection answers belong to
  the context. A persona named after a campaign ("… - Oct 2026") is a sign the offer is inside.
- When a persona is long, tangled or full of history, format it with `format-persona` before
  rewriting anything by hand: it sorts the text into the six sections, removes the history and
  repetition, and moves the offer out. Pass `agent_id` when the persona belongs to that one
  agent, so the offer goes to its context. A long persona takes minutes: while it answers
  `pending`, call it again with the same input.
- Compare the result with the original before showing it: the formatter can drop a rule or
  leave the examples section empty. Show the user the new persona (and the new context if the
  offer moved) with what you would restore, then save it with `save-formatted-persona`, with
  the same input. Fix what it dropped afterwards with `update-persona`. Never retype a long
  persona through `update-persona` to save a formatting.
- When a complaint comes from what the agent's settings do to the message (the settings can
  strip commas, colons or em dashes after the model has written it), the fix is the setting,
  not a rule. A rule asking the model to keep or avoid a character the platform then removes
  cannot work. Check `settings.message_transformations` before writing any rule about
  punctuation, and point the user to the setting. Stripping commas also makes whole replies
  hard to read (« manque de ponctuation » is a common complaint), not only numbers: raise it when the
  complaint is about readability.

## Tone: describe the behaviour, not a ban

The model applies tone rules to the letter. A ban written to stop one tic also removes the
behaviour around it, so write what the agent should do.

- **Bans on reacting make the agent cold.** « Zéro rebond », « aucun rebond libre », or banning
  « Je comprends », « Je vois », « Ok » as openers: the agent then skips past a lead who mentions
  a baby or an illness and fires its next question. When the complaint is « il en fait trop »
  or « il répète », write the behaviour wanted instead: react in a few words to one concrete
  detail of what the lead said, never restate their facts, then move on.
- **A list of words to use becomes a tic.** Connectors given as words to use (« En vrai »,
  « Du coup », « Franchement ») end up in a third of the replies. Give them as an example of the
  register, or write « varie, jamais deux fois le même connecteur ».
- **Warmth is not slang.** « Plus chaleureux » means empathy and reactions specific to what the
  lead said, in the persona's register. Keep the vous and the professional tone if the persona
  has them, and add no familiar words: « C'est calé pour toi ? » was reported as not
  professional.
- **What to do after a first « non » is the agent owner's choice.** « Reframe au moins une fois »
  makes the agent re-pitch to a lead who just said she'd rather wait, and giving up at once is
  complained about too. Ask the user what they want (or what their client wants, if they run
  the agent for someone), then write one gentle question at the first no and a warm exit after
  it, never a second pitch.
- **Check a tone change against what people actually complain about**: the agent asks again
  for something already answered, doesn't answer the question or send the link, gets a fact
  wrong, or writes French that reads oddly. A new style rule rarely fixes any of these.

## Review the agent

`review-agent` gives the studio's analysis of an agent. Run it:

- before working on an agent you don't know, to see what is already wrong;
- after every change to an agent, its persona or its context, and on every agent that uses a
  persona you changed;
- after saving a formatted persona.

Report its findings in plain words. Fix those your change caused, and suggest a fix for the
others rather than applying it unasked.

## Before every write, check

- Does this text already exist elsewhere in the agent? Then edit that, don't add.
- Does it contradict another rule? Settle it now.
- Is any of it history, a transcript, an escalation marker or a note for humans? Remove it.
- Is it in the right place (persona, context, step, edge, setting)?
- Do the dates match their weekdays in that year?
- Is a tone rule written as the behaviour wanted, rather than a ban or a list of words?
- Is the step instruction still a few sentences?
- Has the user seen the final text?
- After the write: will you run `review-agent`?

See `references/anti-patterns.md` for real examples of each drift, taken from production
agents, when you need to show the user why a change is worse than it looks.

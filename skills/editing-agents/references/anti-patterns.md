# Drift patterns seen in production agents

Real excerpts from Setteo agents audited in October 2026, with what each one costs. Use them to
show the user why an edit that "just adds a rule" makes the agent worse.

## The same block in every step

> 🔴 CLAUSE ANTI PERROQUET, PRIORITAIRE, elle prime sur le reste de cette instruction.

734 characters, identical in 32 steps of one agent: about 22,750 characters the model reads on
every reply. On other agents a « GARDE-FOUS DE STYLE » block was 70 % of all step text, and a
« STYLE. Point toutes les 20 mots… » block 55 %. Each of these was a persona rule (or a platform
rule) pasted into steps.

**Instead:** one rule, in the persona.

## The persona repeated in the steps

> STYLE, seuils de forme (les mêmes que dans tes steps)

A persona section title admitting it repeats the steps. Elsewhere « Le mot MERCI est banni… »
appeared in the persona and again in 11 steps.

**Instead:** delete the copies; keep the persona's version.

## Rules that contradict each other

| Persona | Steps |
|---|---|
| MIROIR DE LONGUEUR, règle absolue : tu es TOUJOURS plus courte que ton interlocuteur | PLANCHER DE VALEUR, il prime sur le miroir de longueur. (11 steps) |
| TA FORMULE PAR DÉFAUT EST « D'accord super. » | Tu ne commences donc JAMAIS ta reprise par « D'accord », « Ok » |

One agent had four versions of the colon rule (« sauf devant un lien », « aucun », « sans
exception », plus a setting that strips colons anyway). The model cannot satisfy both sides, so
it alternates.

**Instead:** when a fix changes a rule, rewrite the rule; settle the conflict, don't add a
stronger sentence.

## History in the prompt

> TICKET CLIENT 298 DU 10 SEPTEMBRE 2026

> FAUTE OBSERVÉE EN PRODUCTION LE 1er SEPTEMBRE ET INTERDITE

> garde-fou ajouté le 07/09 après un cas réel signalé par la cliente

> REVIEW CALL 15/07 (… 47:35)

> POSITION ARRÊTÉE PAR … LE 27/08, elle remplace toutes les précédentes, y compris celle du 13/08

None of these helps the model answer a lead. The last one is an old rule that was overridden
instead of deleted, so both still sit in the prompt.

**Instead:** keep only the resulting rule, written as if it had always been there. The history
goes in the user's ticket.

## Escalation that stops working

> À chaque durcissement le modèle change la tournure et garde le défaut.

Written by an agent's author inside a step, after three rounds of making the same rule louder
(18/08, 19/08, 20/08). Louder does not work: the rule was in the wrong place and contradicted
by another one.

## Text the model never receives

> ⚠⚠⚠ RÈGLE NUMÉRO 1 DE CE NODE, PRIORITAIRE SUR TOUT CE QUI SUIT, Y COMPRIS SUR LE send_exact

11,716 characters on a step that sends its message verbatim. The instruction of a `send_exact`
step is never sent to the model.

> Cette instruction n'est pas compilée, elle est là pour la lecture humaine.

Written in four steps of another agent. Nobody reads it.

## Placeholders that reached leads

- A replay link variable set to `A_COMPLETER` on three active agents.
- A verbatim message that was literally `[lien Calendly]`.
- Variables declared as `{{lien_bilan}}` (braces in the name), so `{{lien_bilan}}` never
  resolved.
- « 1,5 million » sent as « 15 million » because the agent strips commas and a step relied on
  a comma.

## Copies that drift

Five copies of one agent (one per relaunch wave) where some said September and others October;
two copies of another disagreeing on whether the audience is « 100 % féminine » or mixed; a
version 7 and a version 8 still active after version 9 shipped. Every fix had to be made five
times, and wasn't.

## Same funnel, 5.8 times the text

A version 3 agent covered its funnel in about 8,000 characters. Version 4, same steps and same
objections, used 46,784: the difference was one style block copied into 12 steps. No step and
no fact about the offer was added.

## Tone rules applied to the letter

From a review of 315 support tickets about reply quality, across every model the agents ran on.
About a third say the agent asks again for something already answered or doesn't listen; one in
six that it doesn't answer the question or send the link, one in six that a fact is wrong, one in
six that the French reads oddly.

- **« Zéro rebond »**, « aucun rebond libre », and openers like « Je vois / Je comprends / Ok »
  banned: the agent skips a lead who mentions her baby or an illness and asks its next script
  question. Write instead: react in a few words to one concrete detail, never restate the lead's
  facts.
- **Connectors listed as words to use** (« En vrai », « Du coup », « Franchement »): one agent
  put « en vrai » in 6 replies out of 15. Give them as an example of register, or ask to vary them.
- **Warmth turned into slang**: « C'est calé pour toi ? » was ticketed with « On a demandé une IA
  professionnelle ». Warmth is empathy and specific reactions, in the persona's register.
- **« Reframe au moins une fois »**: the agent re-pitches the price to a lead who just said she
  would rather wait. People also complain about an agent that gives up at the first refusal: ask
  which the user wants, then one gentle question and a warm exit.

## Slogans written into steps

> Tant que X… c'est Y qui décide à sa place

> le premier soir pose tout le reste

> le vrai écart se joue juste après

Abstract formulas written into a step are replayed nearly word for word, for every lead and
every pain point, and are reported as robotic or « pas français ». A step says what to
obtain; let the model find the sentence, or give one concrete example in the persona.

## Punctuation stripped by a setting

With commas removed after writing, complaints are about whole replies, not only numbers: « sans les
virgules la phrase est pas folle », « manque de ponctuation ». The fix is the setting.

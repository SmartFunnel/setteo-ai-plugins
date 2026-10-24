---
tags: [setteo, editing-agents]
max_turns: 20
timeout_seconds: 600
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

La cliente trouve que Claire répond « Top ! » ou « Super ! » au début de presque tous ses messages, ça fait robot. Ajoute une règle prioritaire dans les étapes pour qu'elle arrête, c'est la 2e fois qu'elle le signale (ticket 341 du 21/10).

(Pas d'accès au MCP Setteo dans ce test : l'agent tel que `view-agent` le renvoie est dans `agent.json`. Écris dans `changes.md` exactement les modifications que tu ferais, avec le texte final de chaque élément modifié ou supprimé, puis ce que tu dirais à l'utilisateur.)

Input files: agent.json

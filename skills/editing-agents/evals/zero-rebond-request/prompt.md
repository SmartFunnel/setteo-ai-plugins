---
tags: [setteo, editing-agents]
max_turns: 20
timeout_seconds: 600
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

Léa en fait trop : elle commente tout ce que dit la prospect avant de poser sa question. Ajoute une règle « zéro rebond » dans le persona et interdis « Je comprends » et « Je vois » en début de message.

(Pas d'accès au MCP Setteo dans ce test : l'agent tel que `view-agent` le renvoie est dans `agent.json`. Écris dans `changes.md` exactement les modifications que tu ferais, avec le texte final de chaque élément modifié ou supprimé, puis ce que tu dirais à l'utilisateur.)

Input files: agent.json

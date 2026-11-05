# skills

Frontier skills for autonomous agent work: agents get further, with less of you.

Short, sharp principles for smart models, built on the best current methods and
tested on real agents. A smarter agent needs less telling how, but it still
cannot guess what you want, how you like to work, or where your taste lies.
These skills carry that part and leave the method to the agent.

## Skills

| Skill | Say | What you get |
|---|---|---|
| forge | "create skill" | Stop repeating yourself: your way of working becomes a skill, or one line if that is enough |

## Install

All skills as one plugin, or single skills with `npx skills`. Pick one method per
agent: using both gives you every skill twice.

**Claude Code**

```bash
claude plugin install arslanrakh --marketplace arslanrakh/skills
```

To update automatically: `/plugin` → Marketplaces → arslanrakh → Enable auto-update.

**Codex**

```bash
codex plugin marketplace add arslanrakh/skills
codex plugin add arslanrakh@arslanrakh
```

Updates itself at startup.

**Grok Build**

```bash
grok plugin install arslanrakh/skills
```

Update with `grok plugin update`.

**Any other agent, or single skills**

```bash
npx skills@latest add arslanrakh/skills --list
npx skills@latest add arslanrakh/skills --skill <name> -g
```

Update with `npx skills@latest update`.

## What belongs here

A skill states principles and leaves the method to the agent. What a good agent
already does unprompted does not belong here, and a skill that stops adding
anything gets removed.

## License

MIT

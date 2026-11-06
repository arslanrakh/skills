# skills

Agent skills, not runbooks. The bet is that each new model needs less telling
how, and still cannot guess what you want, how you like to work, or where your
taste lies.

So a skill here fits on one screen: the standard and the limits. What a good
agent already does unprompted does not belong here, and a skill that stops adding
anything gets removed.

## Skills

| Skill | Say | What it does |
|---|---|---|
| [forge](skills/forge/SKILL.md) | "create skill" | Turns the way you work into a skill your agent can follow. If one line in your agent's instructions is enough, it gives you that instead. |
| [evolve](skills/evolve/SKILL.md) | "evolve" | Improves code, a UI or anything else in rounds: some agents change it, others look for real problems, and the best version stays. |
| [land](skills/land/SKILL.md) | "land" | Finishes a change while you are away: not just merged, but seen working for its users. Anything only a person can do, it reports back. |

## Install

Pick one method per agent: mixing them installs skills twice.

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
grok plugin install arslanrakh/skills --trust
```

Update with `grok plugin update`.

**Any other agent, or single skills**

```bash
npx skills@latest add arslanrakh/skills -g
```

Add `--skill <name>` for a single skill. Update with `npx skills@latest update`.

## License

[MIT](LICENSE)

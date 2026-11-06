# skills

Agent skills, not runbooks. The bet is that each new model needs less telling
how, and still cannot guess what you want, how you like to work, or where your
taste lies.

So a skill here fits on one screen: the standard and the limits. What a good
agent already does unprompted does not belong here, and a skill that stops adding
anything gets removed.

## Skills

| Skill | Say | What you get |
|---|---|---|
| [forge](skills/forge/SKILL.md) | "create skill" | Stop repeating yourself: your way of working, and nothing else, becomes a skill, or one line if that is enough |
| [evolve](skills/evolve/SKILL.md) | "evolve" | Stop settling for the first answer: adversarial rounds push anything further, measured or not, and agreement proves nothing |
| [land](skills/land/SKILL.md) | "land" | Stop watching over changes: one goes past the merge to working for its users while you are away, or you get told what only a person can do |

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

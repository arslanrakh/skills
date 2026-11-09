# Skills for Frontier Models

Agent skills, not runbooks. The bet is that each new model needs less telling
how, and still cannot guess what you want, how you like to work, or where your
taste lies.

So a skill here fits on one screen: the standard and the limits. What a good
agent already does unprompted does not belong here, and a skill that stops adding
anything gets removed.

## Skills

| Skill | Say | What it does |
|---|---|---|
| [to-explainer](skills/to-explainer/SKILL.md) | "to-explainer" | Makes a document about a system, idea or decision that people get at a glance and can retell. Pictures first, the business before the details, in the form the reader needs. |
| [hill-climb](skills/hill-climb/SKILL.md) | "hill-climb" | Improves code, a UI or anything else in rounds. First you agree on the goal, what to compare against and who judges. Then some agents change the work, others look for real problems, and the best version stays. |
| [to-skill](skills/to-skill/SKILL.md) | "to-skill" | Turns the way you work into a skill your agent can follow. If one line in your agent's instructions is enough, it gives you that instead. |
| [ship-afk](skills/ship-afk/SKILL.md) | "ship-afk" | Finishes a change while you are away: not just merged, but seen working for its users. Anything only a person can do, it reports back. |

Each skill runs when you say its name, for example "to-explainer how our payments work" or "hill-climb the landing page to the top 1% for UX".

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

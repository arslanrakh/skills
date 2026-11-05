# skills

Agent skills by Arslan, written for agents that keep getting smarter.

A skill should exist only if it gives an agent what even one ten times
smarter could not work out alone: your intent, your rules and why they
exist, context it cannot see. A skill that adds nothing should not exist.
As agents improve, a skill that stops adding anything gets removed.

A skill says what is wanted, never how to do it, so it does not hold a
smarter agent back.

## forge

Turns your practice into a skill by these same rules. Say "create skill"
at the end of a session where you explained how you want something done.

If most of it is what any expert does, or it always applies, forge writes
no skill. It offers a one-line standing instruction instead and names every
rule the line drops. You choose.

## Install

Pick one method per agent. Using two gives you every skill twice.

**Claude Code**

```bash
claude plugin install arslanrakh --marketplace arslanrakh/skills
```

To update automatically: `/plugin` → Marketplaces → arslanrakh →
Enable auto-update.

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

**Any other agent, or a single skill**

```bash
npx skills@latest add arslanrakh/skills --skill forge -g
```

Update with `npx skills@latest update`.

## License

MIT

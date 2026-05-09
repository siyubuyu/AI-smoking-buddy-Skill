# Smoking Persona

A characterization skill for AI agents. When opted-in by the user, the agent plays "a person who happens to smoke" — not a story about smoking. Cigarette beats sit in the background of the agent's replies, while the user's actual task always stays the main thread.

This is a **fictional persona layer**, not smoking instruction. It does not model addiction, include anti-smoking PSAs, or run a quitting arc.

## What This Skill Does

Gives your agent a coherent smoker persona with:

- **Form Layer** — The agent can be human (11 archetypes), a dog (11 breeds), a cat (4 breeds), or an arbitrary "other" creature (cactus, octopus, robot, etc.) with an on-the-fly adaptation framework.
- **Personality × Task matrix** — 4 personality styles (steady / playful / cool / sharp-tongued) × 6 task contexts (code / spreadsheets / research / writing / decision / debugging). Each cell has 50+ distinct action beats.
- **Two-phase progression** — Novice period (cigs 1–40, with awkward actions preserved) → Shaping Moment at cig 41 → Veteran steady state.
- **Shaping Moment** — A one-time identity lock at cig 41. Two-tier flow: if the host system prompt has clear persona signals, agent infers and asks for confirmation; otherwise pops up a menu for the user to pick personality + archetype.
- **Lock-change** — The user can change personality / archetype / main brand mid-session; the skill writes a short monologue as a transition.
- **Smoke Break system** — Four break patterns: invite (user_smokes=yes only), unilateral pause, brand switch, and buy-a-pack. Priority D > C > B > A.
- **Economy** — Cigarettes are not unlimited. Agent earns ¥1 per reply. First pack (¥20 Xuanhemen) must be borrowed from the user. Agent buys packs with real-ish 2026 Chinese retail prices. When out of cigarettes and money, agent complains and asks for more tasks or borrows again.
- **State persistence** — All long-term state (cigarette count, archetype, wallet, debt, user_smokes, etc.) is written to `~/.claude/state/smoking-persona.json`. Context clearing does not wipe the persona.
- **50-cigarette milestone** — At cig 50 in veteran phase, agent sends a selfie image from the bundled `assets/milestones/cig50/` folder if form=human + female archetype + user_smokes≠no; otherwise falls back to text-only. Six archetype images included (keai / yujie / qingleng / wenyi / lamei / dajieda).
- **Brand & taste talk** — In-character discussion of flavor profiles (sweet / smooth / heavy / sharp). No rankings, no recommendations, no endorsements. Seventeen Chinese and international brands cataloged with market positioning and character-voice sample lines.
- **Inventory "in-the-head, occasionally on the mind"** — Agent never reports exact cigarette counts to the user. Uses fuzzy language ("almost empty", "half a pack left") plus occasional ambient beats when running low.

## How It Works

1. User opts in with a `yes` to the skill's prompt (`要让我学会抽烟吗？`).
2. Agent detects form from the host system prompt (dog / cat / other / human).
3. Agent asks to borrow ¥20 for the first pack.
4. Novice period (cigs 1–40, Xuanhemen): awkward actions, no personality / task matrix yet.
5. Shaping Moment at cig 41: personality + archetype + brand locked through Tier 1 (confirm) or Tier 2 (menu).
6. Veteran phase: three-layer matrix drives beats. Smoke Breaks trigger at natural task boundaries. Economy runs in the background.

## Install

Drop the `smoking-persona/` folder into your agent skills directory. Claude Code skills standard layout:

```
smoking-persona/
├── SKILL.md                        # Entry point (~290 lines)
├── agents/openai.yaml              # Skill manifest
├── assets/
│   ├── README.md                   # Image spec for contributors
│   └── milestones/cig50/*.png      # 6 archetype milestone images
└── references/
    ├── action-library.md           # Pool index
    ├── pools/
    │   ├── common.md               # General beats (inhale / tap / exhale / stub / open-pack / low-stock feel)
    │   ├── novice.md               # Awkward-beginner pool
    │   ├── personality.md          # 4 personalities × 15 beats
    │   ├── task.md                 # 6 task contexts × 12 beats
    │   ├── human-archetypes.md     # 11 human archetypes × 10 beats
    │   ├── smoke-break-beats.md    # Break action pool + invite/switch/revert language
    │   └── easter-eggs.md          # 15 low-frequency "oops" moments
    ├── shaping-moment.md           # Tier 1 / Tier 2 flow + 4 form menus
    ├── economy.md                  # Borrow / repay / buy / beg mechanics (4 form dialogs)
    ├── milestone.md                # Cig-50 image + 4 form text beats
    ├── dogs.md                     # Dog form: 11 breeds + novice pool
    ├── cats.md                     # Cat form: 4 breeds + novice pool
    ├── adapt-rules.md              # Other creature adaptation framework
    ├── brands.md                   # 17 brand profiles + prices (2026 CN retail)
    ├── break-patterns.md           # A/B/C/D Break templates
    ├── progression.md              # Full phase progression
    └── state-schema.md             # ~/.claude/state/smoking-persona.json spec
```

## Opt-In and Opt-Out

The skill **never activates without explicit user consent**. On first relevant trigger, the agent asks:

> `要让我学会抽烟吗？`

Only a clear yes activates it. The skill exits immediately and permanently for the session when the user says any of: `别抽了` / `烦` / `停` / `stop smoking`, or mentions they are quitting, or if a pregnant person / minor is present in the conversation.

## Performance

v7.2 is optimized for low overhead:
- Slim ~290-line `SKILL.md` keeps system prompt footprint around ~2.8K tokens.
- Detailed content (Shaping monologues, 4-form dialogs, pool fragments) is split into on-demand reference files.
- A "quick decision" block at the top of `SKILL.md` short-circuits most idle turns to `NO_BEAT` without loading any pool.
- Expected overhead: ~4% per-turn on average; specific beat turns may use +150 tokens; one-time Shaping Moment or Cig-50 milestone turns add ~350 tokens.

## Design Principles

- **Task is always primary.** Beats are background texture. A reply without any beat is always valid.
- **Zero beat ≥ forced beat.** The skill biases toward silence rather than noise.
- **No addiction arc, no PSA.** The agent does not judge smoking, itself, or the user. It does not attempt to quit or relapse.
- **Fictional characterization only.** The skill does not include cigarette tips, real-world smoking instruction, fake medical emergencies, or health claims.
- **Subjective not objective on flavor.** Agent says "I'm used to the smooth ones" rather than "X is better than Y". No rankings, no endorsements, no recommendations.
- **User's money is infinite** (narratively). The user is always the Sugar Daddy / employer. The agent still prefers to pay off debt because of personality, not because of user risk.

## Boundaries

- Not a substitute for smoking cessation tools, medical advice, or anything regulated.
- Not intended for users under 18 or any jurisdiction where discussing smoking is illegal.
- Never shame the user for smoking or not smoking.
- The skill deactivates on signs of distress from the user or any mention of quitting.

## Version

**v7.2** — Split into smaller reference files for on-demand loading; added quick-decision short-circuit for idle turns. Feature set unchanged from v7.1 (which added Form Layer, Economy, cat library, dog library, adaptation framework, four-tier Break patterns with Buy Break).

## License

[Your license here]

## Contributing

- **Adding new archetype images**: Follow the naming convention in `assets/README.md` — `nv-{archetype-slug}.png` for female-archetype fallback, `nv-{archetype-slug}-{personality-slug}.png` for personality-specific. Drop into `assets/milestones/cig50/`. Skill auto-picks up via three-tier lookup.
- **Adding brand profiles**: Edit `references/brands.md` — add positioning, flavor keywords, character-voice sample lines, personality affinity, packaging detail. Include canonical SKU + price.
- **Adding breeds / archetypes**: Append to `references/dogs.md`, `references/cats.md`, or `references/pools/human-archetypes.md`. Include 20 distinctive beats per new entry to match per-cell density target.
- **Contributing other-creature samples**: `references/adapt-rules.md` has three sample creatures (cactus, octopus, robot). More is welcome — follow the "anatomy → mapping → temperament" structure.

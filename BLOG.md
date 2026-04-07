# When the Music Changes, the Rhythm Must Change Too

## How I Cut My Claude Code Usage from 100% to ~12% — and Got Better Results

---

*A few weeks ago, I posted what became my most viral take ever: a thread calling out Claude Code's pricing as a scam. 200K+ impressions on X, 100+ upvotes on Reddit. The response was overwhelming — and it told me I wasn't alone.*

*But this isn't that post. This is what I did next.*

---

### The $20 wake-up call

Here's the context: I'm building a mobile app ([FLAI Speaking by Flalingo](https://flalingo.com)) — a daily AI speaking coach for English learners. React Native, Expo, Fastify backend, ElevenLabs voice AI. Real product, real users, real deadlines.

I was doing everything through Claude Code. Planning, coding, reviewing, testing — all Claude, all the time. It felt productive. Then I bought $50 in API credits just to test something.

Two code reviews later — not even complex ones — **$20 gone**. Sonnet 4.6, not Opus. Fresh session.

I ran the exact same review through OpenCode with Opus 4.6 at max effort. Cost: **$5.30**. And the findings were more detailed.

That was the moment I realized: I wasn't using AI wrong. I was using the *wrong AI for the wrong job*.

---

### The old way: one model does everything

Most developers using Claude Code today have the same workflow I had:

```
User → Claude Code (Opus/Sonnet) → Code → Review → Test → Ship
```

Every token, every thought, every boilerplate function — all running through the most expensive model available. It's like hiring a principal engineer to write unit tests and rename variables.

We do it because it's *easy*. One tool, one context, one conversation. But easy isn't cheap, and cheap isn't optional when you're shipping a real product on a real budget.

---

### When the music changes, the rhythm must change too

This is a Turkish saying my grandmother used to say. *Müzik değişince ritim de değişir.* When the conditions change, your approach must follow.

The conditions changed. Hard.

- Claude Code got expensive fast — and opaque about why
- Local models got good. Really good. Qwen3-Coder, Gemma 4, MiniMax M2.7
- Multi-agent tooling matured — OpenCode, Codex CLI, MCP protocols
- The "one model does everything" era ended

So I stopped fighting the old rhythm and built a new one.

---

### The missing piece most people skip: specs

But before I talk about multi-agent dispatch, I need to talk about the mistake I see *everyone* making — including my past self.

**People jump straight to code.**

"Build me an onboarding flow." And the AI starts writing React components. No requirements. No technical plan. No thought about edge cases, API contracts, or how this fits the existing architecture.

For a bug fix or a config change, sure — just do it. But for anything substantial? You're building on sand.

Here's what I learned building a full product with AI agents: **the bigger the task, the more you need to think before you code.** This isn't a new idea — it's how real engineering teams work. The AI era didn't change that. It just made it easier to skip, because the code comes so fast.

So I built a triage system:

```
┌─────────────────────────────────────────────────────┐
│                  TASK TRIAGE                         │
├──────────┬──────────────────────────────────────────┤
│ S/M      │ Bug fix, new screen, simple feature      │
│          │ → Skip specs. Dispatch agent. Ship.      │
├──────────┼──────────────────────────────────────────┤
│ L        │ New feature spanning multiple modules     │
│          │ → Write a Technical Spec (TSD) first     │
│          │ → Then code against the spec             │
├──────────┼──────────────────────────────────────────┤
│ XL       │ New product area, major feature set       │
│          │ → Write Functional Spec (FSD) first      │
│          │ → Then Technical Spec (TSD)              │
│          │ → Then code against both                 │
└──────────┴──────────────────────────────────────────┘
```

**FSD** answers: *What are we building and why?* User goals, functional requirements, user flows, edge cases, non-goals, success metrics.

**TSD** answers: *How are we building it?* Architecture, API contracts, data models, state management, integration points, implementation plan with agent assignments.

The beauty is: **the specs themselves get written by agents too.** I don't write them by hand. I dispatch GPT 5.4 or MiniMax to draft the spec, I review and approve it, and *then* the coding agents have a clear target to build against.

This single change — specs before code for big tasks — eliminated 80% of the "it built the wrong thing" moments that were costing me entire days of rework.

---

### The new architecture: Claude as PM, not as engineer

Here's the mental shift that changed everything:

**Claude doesn't write code anymore. Claude manages the team that writes code.**

I restructured my entire workflow around a single idea: **Claude Code is the Technical PM. It triages tasks, writes specs when needed, dispatches agents, reviews the reviews. But it rarely touches the keyboard itself.**

The actual coding? That goes to cheaper, faster, often *local* models — through MCP tools that Claude can call directly.

```
User → Claude Code (PM)
         │
         ├── Triage: How big is this task?
         │
         ├── [XL] Write FSD → approve → Write TSD → approve
         ├── [L]  Write TSD → approve
         ├── [S/M] Skip specs
         │
         ├── opencode_fire → Qwen3-Coder (local, FREE) → writes code
         ├── opencode_fire → MiniMax-M2.7 → reviews code
         ├── codex → GPT 5.4 → complex review
         ├── opencode_fire → Gemma-4 (local, FREE) → writes tests
         └── Claude subagent (Sonnet/Opus) → only for architecture decisions
```

---

### The agent lineup

I treat my AI agents like a real engineering team with seniority levels:

| Role | Agent | Cost | When to use |
|------|-------|------|-------------|
| Junior Dev | Gemma-4-31b-it | **Free** (local) | Boilerplate, tests, simple fixes |
| Mid Dev | Qwen3-Coder | **Free** (local) | General coding, feature implementation |
| Mid Dev | MiniMax-M2.7 | Low | Coding + code review + spec writing |
| Mid Dev | Qwen3.6 Plus | Free | Coding + code review + spec writing |
| Senior Dev | GPT 5.4 Codex | Medium | Complex tasks, deep review, FSD/TSD |
| Senior Dev | Sonnet 4.6 | High | Cross-cutting complex work |
| Principal | Opus 4.6 | **Highest** | Architecture decisions only |

**The rule is dead simple: always pick the cheapest model that can do the job.** A junior dev doesn't need to be a principal engineer. A variable rename doesn't need Opus. A spec draft doesn't need Anthropic.

---

### The pipeline: every task, every time

No exceptions. Every piece of code goes through this:

```
1. CODE    → Cheapest suitable agent writes the code
2. REVIEW  → Different agent reviews (writer ≠ reviewer, always)
3. TEST    → Automated tests run (pnpm validate)
4. MANUAL  → Junior PM agent does UX review from user perspective  
5. MERGE   → Only if everything passes
```

The key insight: **the reviewer must always be a different agent than the writer.** This isn't just process theater — different models catch different things. MiniMax catches what Qwen misses. GPT 5.4 catches what MiniMax misses. It's genuine diversity of thought, at machine speed.

For large tasks, the TSD's implementation plan drives step 1 — each sub-task gets dispatched to its assigned agent, and steps 2-5 apply to each.

---

### How it actually works — two real examples

**Example 1: Small task (Tier S)**

*"Fix the auth token refresh race condition."*

Claude Code triages this as S — simple bug fix. No specs needed. It dispatches Qwen3-Coder to fix it, MiniMax reviews the fix, tests run, done. Total Anthropic cost: near zero.

**Example 2: Large task (Tier XL)**

*"Build the complete onboarding flow with voice diagnostic, analysis, and results."*

Claude Code triages this as XL — new user-facing flow across multiple modules.

1. Dispatches GPT 5.4 to write the FSD — user goals, screen flows, edge cases
2. I review and approve the FSD
3. Dispatches MiniMax to write the TSD — API contracts, state management, component breakdown, implementation plan
4. I review and approve the TSD
5. TSD has 8 sub-tasks. Claude dispatches them:
   - 4 tasks to Qwen3-Coder (local, free) — in parallel
   - 2 tasks to MiniMax (low cost)
   - 2 tasks to GPT 5.4 (complex integration logic)
6. Each completed task gets reviewed by a *different* agent
7. Tests run after each merge
8. Gemma-4 does final UX review from user perspective

The whole feature ships in a day. Anthropic token usage? **Only the triage decisions and orchestration.** Maybe 5% of the total work.

Without the FSD/TSD step, this same feature would have taken 3 days of rework because the first agent would have built the wrong state machine, the second would have misunderstood the API contract, and I'd be debugging integration issues instead of shipping.

---

### The numbers

Before this approach:
- **100% Anthropic usage** for all coding work
- $20 burned on two code reviews
- Constant hitting weekly limits on Max Plan
- Daily cost anxiety
- Large features took days of rework — "it built the wrong thing"

After:
- **~12-15% Anthropic usage** — only for orchestration + rare complex decisions
- Local models handle 60-70% of coding (Qwen3-Coder, Gemma-4) — **completely free**
- MiniMax and Qwen3.6 handle another 15-20% — minimal cost
- GPT 5.4 Codex handles complex reviews and specs — moderate cost
- Opus/Sonnet only for architecture decisions — rare
- Large features ship right the first time because specs exist

**Same output. Better reviews. Specs that prevent rework. Fraction of the cost.**

---

### I open-sourced the workflow

I packaged this entire approach as an installable skill for Claude Code:

```bash
npx skills add htuzel/technical-pm
```

Once installed, Claude Code automatically:
- **Triages every task** into S/M/L/XL complexity tiers
- **Writes FSD/TSD specs** for large tasks before any code is written
- **Dispatches to the cheapest suitable agent** for both specs and code
- **Enforces the CODE → REVIEW → TEST → MANUAL → MERGE pipeline**
- Runs independent tasks in parallel
- Only uses Anthropic tokens for orchestration

It's one command. The skill teaches Claude *how to think about what to build before building it*, and then *how to manage the team* instead of doing everything itself.

---

### What I learned

**1. Specs aren't bureaucracy — they're the cheapest debugging tool you have.**

A 15-minute spec review catches architecture mistakes that would cost a full day of rework. When your coding agents are fast, building the wrong thing fast is worse than building the right thing slightly slower.

**2. The best model isn't the most expensive one — it's the right one for the task.**

A $0 local model writing a React component is not worse than Opus writing it. It's often the same quality. The tokens don't care about the price tag.

**3. AI agent diversity beats AI agent power.**

One Opus doing everything < Qwen writing + MiniMax reviewing + GPT doing deep review. Different architectures, different training data, different blind spots. The review chain catches more bugs than any single model ever could.

**4. The "PM pattern" is the future of AI-assisted coding.**

We're heading toward a world where the human (or the most capable AI) is the coordinator, not the executor. Triage, spec, dispatch, review. The sooner you internalize this, the sooner your costs drop and your quality rises.

**5. Vendor lock-in is the real scam.**

My anger at Anthropic's pricing was legitimate. But the real mistake was giving them 100% of my workflow. Now if Claude doubles in price tomorrow, I shrug. 85% of my work doesn't touch their servers.

---

### The Turkish grandmother was right

When the music changes, the rhythm must change too.

The music changed: AI models got cheaper, local models got better, multi-agent tooling matured, and single-vendor pricing became unsustainable.

My rhythm changed: Claude stopped coding and started managing. Specs come before code. The cheapest capable agent gets the job. Every review is done by a different model.

Your rhythm should change too.

---

*The technical-pm skill is open source: [github.com/htuzel/technical-pm](https://github.com/htuzel/technical-pm)*

*Previously: [My viral post about Claude Code's pricing](https://x.com/) — 200K+ impressions, the post that started this journey.*

---

**If you're still running everything through a single AI model with no specs and no triage, you're paying 2024 prices for a 2024 workflow in a 2026 world. The music changed. Are you still dancing to the old beat?**

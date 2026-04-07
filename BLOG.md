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

### The new architecture: Claude as PM, not as engineer

Here's the mental shift that changed everything:

**Claude doesn't write code anymore. Claude manages the team that writes code.**

I restructured my entire workflow around a single idea: **Claude Code is the Technical PM. It coordinates, it dispatches, it reviews the reviews. But it doesn't touch the keyboard.**

The actual coding? That goes to cheaper, faster, often *local* models — through MCP tools that Claude can call directly.

```
User → Claude Code (PM)
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
| Mid Dev | MiniMax-M2.7 | Low | Coding + code review |
| Mid Dev | Qwen3.6 Plus | Free | Coding + code review |
| Senior Dev | GPT 5.4 Codex | Medium | Complex tasks, deep review |
| Senior Dev | Sonnet 4.6 | High | Cross-cutting complex work |
| Principal | Opus 4.6 | **Highest** | Architecture decisions only |

**The rule is dead simple: always pick the cheapest model that can do the job.** A junior dev doesn't need to be a principal engineer. A variable rename doesn't need Opus.

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

---

### How it actually works — the dispatch

This isn't theoretical. Here's what a real task looks like in my terminal:

I tell Claude Code: *"Implement the onboarding analytics events."*

Claude Code (as PM) doesn't write a single line. Instead:

1. It calls `opencode_fire` to dispatch Qwen3-Coder for the implementation
2. While that runs, it calls `opencode_fire` again to have Gemma-4 write the test stubs — **in parallel**
3. When the code is done, it calls `opencode_review_changes` and dispatches MiniMax-M2.7 for review
4. If the review flags issues, it dispatches Qwen3-Coder again for fixes
5. Runs `pnpm validate`
6. Dispatches Gemma-4 as "Junior PM" for UX sanity check
7. Only then: merge

Claude's Anthropic token usage in this entire flow? **Near zero.** It only consumed tokens for the orchestration decisions — which model to pick, what to dispatch, how to interpret the review.

---

### The numbers

Before this approach:
- **100% Anthropic usage** for all coding work
- $20 burned on two code reviews
- Constant hitting weekly limits on Max Plan
- Daily cost anxiety

After:
- **~12-15% Anthropic usage** — only for orchestration + rare complex decisions
- Local models handle 60-70% of coding (Qwen3-Coder, Gemma-4) — **completely free**
- MiniMax and Qwen3.6 handle another 15-20% — minimal cost
- GPT 5.4 Codex handles complex reviews — moderate cost
- Opus/Sonnet only for architecture decisions — rare

**Same output. Better reviews. Fraction of the cost.**

---

### I open-sourced the workflow

I packaged this entire approach as an installable skill for Claude Code:

```bash
npx skills add htuzel/technical-pm
```

Once installed, Claude Code automatically:
- Acts as Technical PM instead of doing everything itself
- Dispatches to the cheapest suitable agent
- Enforces the CODE → REVIEW → TEST → MANUAL → MERGE pipeline
- Runs independent tasks in parallel
- Only uses Anthropic tokens for orchestration

It's one command. The skill teaches Claude *how to manage* instead of *how to code*.

---

### What I learned

**1. The best model isn't the most expensive one — it's the right one for the task.**

A $0 local model writing a React component is not worse than Opus writing it. It's often the same quality. The tokens don't care about the price tag.

**2. AI agent diversity beats AI agent power.**

One Opus doing everything < Qwen writing + MiniMax reviewing + GPT doing deep review. Different architectures, different training data, different blind spots. The review chain catches more bugs than any single model ever could.

**3. The "PM pattern" is the future of AI-assisted coding.**

We're heading toward a world where the human (or the most capable AI) is the coordinator, not the executor. The sooner you internalize this, the sooner your costs drop and your quality rises.

**4. Vendor lock-in is the real scam.**

My anger at Anthropic's pricing was legitimate. But the real mistake was giving them 100% of my workflow. Now if Claude doubles in price tomorrow, I shrug. 85% of my work doesn't touch their servers.

---

### The Turkish grandmother was right

When the music changes, the rhythm must change too.

The music changed: AI models got cheaper, local models got better, multi-agent tooling matured, and single-vendor pricing became unsustainable.

My rhythm changed: Claude became the conductor, not the entire orchestra.

Your rhythm should change too.

---

*The technical-pm skill is open source: [github.com/htuzel/technical-pm](https://github.com/htuzel/technical-pm)*

*Previously: [My viral post about Claude Code's pricing](https://x.com/) — 200K+ impressions, the post that started this journey.*

---

**If you're still running everything through a single AI model, you're paying 2024 prices for a 2024 workflow in a 2026 world. The music changed. Are you still dancing to the old beat?**

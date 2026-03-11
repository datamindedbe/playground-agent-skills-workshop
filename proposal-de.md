# Context Engineering for Data Engineers — Skills That Fix Your Pipeline

**Infra:** OV Bedrock | **Prep:** Jesus + Jan | **Proposed date:** TBD

---

## Outcomes

| | Ideally, we want to achieve this... |
|---|---|
| **Mental model** | What context engineering is and why it matters for data engineering workflows |
| **Practical skill** | Can read, complete, and use a skill to solve a real data pipeline problem |
| **Positioning** | When a skill makes your agent dramatically better vs when raw prompting is enough |
| **Artifact** | Two working skills they completed + a before/after experience they can explain |

---

## Part 1: Theory

**Arc:** Feel the pain → understand why → see the fix → learn the tool → go fix your own.

#### Block 1 — The Pipeline Problem Demo

- A data pipeline has a quality issue. Claude Code tries to help *without* a skill — suggests a quick fix (`df.drop_nulls()`), silently drops data, moves on.
- Same problem *with* a skill — diagnoses the root cause, explains *why* the nulls happen, preserves the raw data, and applies the fix the Dataminded way.
- The contrast isn't "broken vs working" — it's "quick hack vs the right way." The skill encodes engineering principles, not just code patterns.
- **Prep:** One polished before/after demo using the IoT pipeline on OV Bedrock. Pre-recorded backup.

#### Block 2 — Context Engineering: The Mental Model

- LLMs are capable but directionless without context. "A brilliant intern on day one — smart, but doesn't know your stack, your data, or your patterns."
- Context engineering = designing the information diet for your agent. Three levers: *what info*, *when it appears*, *how it's structured*.
- Data engineering example: Claude knows Python, but does it know your team's principles? At Dataminded: never silently drop rows, explain root causes, validate before loading. Without a skill, Claude gives quick fixes. With a skill, Claude works *the way your team works*.
- The spectrum: Raw prompting → CLAUDE.md → Skills → MCP → RAG. Skills are the sweet spot for *reusable, shareable, scoped* context. (Mention: DataMindy does this with a full MCP server — skills do a lighter version with zero infrastructure.)

#### Block 3 — Skills: What They Are and Why They Work

- **What is a skill?** A markdown file that gives Claude scoped, reusable context. Walk through a real SKILL.md: frontmatter, instructions, `$ARGUMENTS`, `!command` syntax.
- **Why not just paste instructions in chat?** Skills are shareable, version-controlled, and activate only when needed. "A CLAUDE.md is always on. A skill is on-demand."
- **Good vs bad skill:** Side-by-side. A skill that encodes Dataminded engineering principles (never silently drop data, explain the "why", validate before loading) vs a vague "help me with data" prompt.

#### Block 4 — Live Build Together

- Build a simple data quality skill from scratch. Everyone follows along.
- The skill teaches Claude how to check a Polars DataFrame for common issues (nulls, type mismatches, unexpected ranges).
- **Prep:** Starter template ready, tested on OV Bedrock. Pre-recorded backup.

#### Block 5 — Hacking Setup

- Environment check (devcontainer), overview of the pipeline, overview of the exercises, Claude Code cheat sheet for beginners, questions.

---

## Part 2: Hacking

### The Pipeline

Everyone works with the same IoT sensor pipeline:

- **Stack:** Python + Polars + DuckDB (all local, no infrastructure)
- **Pipeline:** Read CSV batch → transform (aggregate, clean) → load into DuckDB → query results
- **Data:** Pre-generated IoT sensor readings (device_id, timestamp, temperature, humidity)
- **Three batches in the repo:**
  - `batch_1.csv` — Clean data. Pipeline runs end-to-end. ✓
  - `batch_2.csv` — Contains null readings (simulating dying sensor batteries). Pipeline crashes on aggregation. ✗
  - `batch_3.csv` — Contains duplicate readings (simulating network retries). Metrics are silently inflated. ✗

---

#### Scenario 1: The Null Crash

**The problem:** New sensor batch arrives. Pipeline crashes because temperature readings have nulls.

**Without a skill:**
- Use Claude Code to debug and fix the pipeline.
- Claude suggests a quick fix: `df.drop_nulls()` or fill with zeros. It "works" but silently loses data — violating Dataminded's principle of never silently dropping rows.

**With a skill:**
- Complete a scaffolded skill (`/data-doctor`). The skill structure and logic are provided — juniors sharpen the prompt sections to encode Dataminded engineering principles: never silently drop data, explain the root cause (battery death), log what's removed and why, validate before loading.
- Run the skill. It diagnoses which columns and rows are affected, explains *why* (battery context), and applies a principled fix — not just a quick one.

**Scaffold approach:** Logic given, prompting blank (Option B). Juniors edit generic prompt sections into specific, principle-driven instructions.

**Key contrast:** The before/after isn't crash vs fix — it's "quick hack" vs "the Dataminded way."

**Deliverable:** Working `/data-doctor` skill that diagnoses and fixes null issues.

---

#### Scenario 2: The Silent Duplicates

**The problem:** Another batch arrives. Pipeline runs without errors — but metrics are wrong. Duplicate readings inflated the averages.

**Without a skill:**
- Use Claude Code to figure out why the numbers look off.
- Harder than scenario 1 — there's no crash, just wrong results. Claude doesn't know to check for duplicates from network retries, and may suggest generic fixes that don't address the root cause.

**With a skill:**
- Complete a scaffolded skill (`/dedup-sensor`). The skill frontmatter and section structure are provided — juniors write the actual instructions telling Claude how to detect duplicates (by device_id + timestamp), explain the cause (network retries), and remove them while preserving audit traceability.
- Run the skill. It produces a structured diagnosis and a principled fix.

**Scaffold approach:** Structure given, logic blank (Option A). Juniors write the instructions themselves, having learned the pattern from scenario 1.

**Fallback:** If the room struggles, this becomes a live build-together exercise instead of self-guided. Both are valid learning experiences — no extra prep needed.

**Deliverable:** Working `/dedup-sensor` skill that detects and removes duplicate readings.

---

#### Stretch Goals

For fast finishers:

| Goal | Description |
|---|---|
| **Combine both skills** | Create a single `/pipeline-guardian` skill that runs both checks in sequence |
| **Add a new scenario** | Generate a `batch_4.csv` with a schema change (new `humidity` column) and build a skill to handle it |
| **Benchmark the difference** | Measure token usage and output quality with vs without skills — quantify the value |

---

## Prep Checklist

- [ ] IoT sensor data generator script (produces batch_1, batch_2, batch_3)
- [ ] Working pipeline code (Polars + DuckDB)
- [ ] Skill scaffolds for `/data-doctor` (Option B) and `/dedup-sensor` (Option A)
- [ ] Devcontainer with Python, Polars, DuckDB, Claude Code pre-installed
- [ ] Before/after demo for Block 1, tested on OV Bedrock
- [ ] Pre-recorded backup of the demo
- [ ] Claude Code cheat sheet for beginners

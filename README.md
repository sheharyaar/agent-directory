# Prompt Directory

Prompts I actually use, kept in one place so I can drop them into whatever agent
I'm working with. Two kinds live here: **voice prompts** (make the output sound
like me), and **workflow prompts** (make the agent behave a certain way across a
whole project — teaching me, or building a document).

Everything is plain markdown. There's no tooling, no build step. You paste a file
into a system prompt, save it as `AGENT.md`/`CLAUDE.md` in a repo, or hand it over
as context.

---

## Voice — [`style-prompts/`](./style-prompts/)

Derived from my own writing at [sheharyaar.in/blog](https://www.sheharyaar.in/blog),
by analysing that voice against default LLM prose. All three share the same four
non-negotiables (personal motivation, an explicit depth/scope choice, rhetorical
questions that get answered, informality) and the same banned-LLM-tic list. They
differ in what the agent is allowed to improve past my own habits.

| File | Use it for |
|---|---|
| [`technical-blog.md`](./style-prompts/technical-blog.md) | Exploratory posts — I chase a question I genuinely had and report what I found. Narrative carries it; it's not a manual. |
| [`technical-tutorial.md`](./style-prompts/technical-tutorial.md) | Step-by-step guides where the reader must *do the thing* by the end. Contract is correctness and reproducibility. |
| [`codebase-agent-interaction.md`](./style-prompts/codebase-agent-interaction.md) | How an agent talks to me inside a repo: chat, plans, clarifying questions, commits, PRs, code comments. Adds an honesty section — admit limits, report failures, don't claim done until verified. |

See [`style-prompts/README.md`](./style-prompts/README.md) for the shared spine and
how they were built.

---

## Learning workflows

### [`SELF_STUDY.md`](./SELF_STUDY.md) — study companion for a book

Drop this in a repo as `AGENT.md` when you're working through a textbook and want
to learn by **implementing, benchmarking and testing** the concepts yourself. Its
golden rule is that the agent *never writes the implementation* — it writes
Makefiles, harness scaffolding, READMEs and build-error diagnosis; you write the
lock, the queue, the CAS loop. It also specifies the repo layout, a sanitizer-first
build system, per-concept README structure, `GLOSSARY.md` discipline (a term only
lands once you can define it unaided), ADR-style `learning-records/`, and a
blog-export path so the tree can become a tutorial series later.

Written for *The Art of Multiprocessor Programming* (C implementations), but the
shape adapts to any book-plus-code topic — swap the book, the language and the
concept list.

### [`BLOG_STYLE_LEARNING.md`](./BLOG_STYLE_LEARNING.md) — visual dossier builder

For **purposeful learning of a large topic**, quiz included. A spec for producing
a **chaptered, diagram-rich learning document** on one topic, as a single
self-contained HTML file. 8–12 chapters in dependency order, one
narrative spine followed end to end ("the life of a Virtual Function", "the life
of one handshake"), a Mermaid diagram per chapter, a cumulative glossary, and
check-yourself questions with answers behind toggles.

The parts worth stealing even if you never build a dossier:

- **Progressive vocabulary** — never use a term before defining it. If a chapter
  needs a term from a later chapter, reorder the chapters; don't hand-wave.
- **Verify references before writing**, and say which URLs you couldn't verify.
- **A scripted self-audit** (§7) that counts structure, parse-checks Mermaid, and
  greps for terms used before their defining section.
- **Probe after delivery** (§8) — quiz the reader through the harness's real
  question tool, never pasted A/B/C/D, and score the *reasoning*, not the letter.
  Findings go back into the document as a dated revision card.

The quizzing follows the **Alvar method** — it hooks into the `probe` / `teach` /
`learn-visual` / `learn-verify` skills when they're installed, and falls back to
the same protocol inline when they aren't.

---

## Output theme

### [`SINGLE_DOSSIER_THEME.md`](./SINGLE_DOSSIER_THEME.md) — the "field dossier" look

For **quick learnings** — one dense document I want to finish in a sitting: an
audit, a comparison, a packet walk, a score card. No chapters, no quiz, no
narrative spine. This is the theme, not the writing spec.

One self-contained HTML file, light only, paper-white and ink-black, six muted
categorical colours on a strict three-step rule (`-l` fills, `-d` lines, `-x`
text), Archivo + IBM Plex Mono, print-safe. No framework, no JS, no build step.
Covers tokens, base rules and each component — verdict stamps, score-card tables,
figure shells, sticky TOC plus side rail, packet-walk lanes — and ends with a
pre-ship checklist.

**This and `BLOG_STYLE_LEARNING.md` are two different looks on purpose**, because
they serve two different jobs: quick learning versus purposeful learning of a
large topic. Pick one per file and don't blend their stylesheets.

---

## Picking one

| I want to… | Reach for |
|---|---|
| Write a post or guide in my voice | `style-prompts/technical-blog.md` or `technical-tutorial.md` |
| Set the rules for an agent working in my repo | `style-prompts/codebase-agent-interaction.md` |
| Learn a topic by building it, from a book | `SELF_STUDY.md` |
| Learn a large topic properly — chapters, diagrams, quiz | `BLOG_STYLE_LEARNING.md` |
| Capture a quick learning or an audit as one document | `SINGLE_DOSSIER_THEME.md` |

---

## TODO

### More learning paths

The two that exist are book-based study (`SELF_STUDY.md`) and purposeful learning
of one large topic (`BLOG_STYLE_LEARNING.md`). Coming:

- [ ] **Project-assisted learning.** The project is the main thing; dossiers are
      where I record what I learned and what I'd improve, not the driver.
- [ ] **Book-assisted learning dossier.** Inverse of the above — the book, its
      quizzes and its exercises drive, and the dossier follows along.
- [ ] **Research-paper implementor.** The agent first helps me write the version
      the paper wants to improve on — so I actually feel the problem it's solving
      — and only then works toward the paper's solution.

### Other prompts

- [ ] **Product research** prompt.
- [ ] **Bug analysis** prompt.

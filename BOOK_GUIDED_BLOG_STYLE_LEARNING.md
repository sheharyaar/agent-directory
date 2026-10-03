# BOOK_GUIDED_BLOG_STYLE_LEARNING.md — Book-Guided Dossier Builder

A reusable spec for turning **a book you are reading** into a folder of blog-style HTML pages, one per chapter, that you read on both sides of the book: a short primer before the chapter, then the chapter itself, then a consolidation page with worked examples, real systems explained for newcomers, hands-on exercises, the book's own questions answered, and an Alvar quiz before and after the reading.

It is the book-driven sibling of `BLOG_STYLE_LEARNING.md`. The voice rules, the vocabulary discipline, the diagram rules, the answer toggles and the quiz protocol come from there and are restated here, so this file works alone. The difference is that **the book sets the order, the scope and the facts.** The agent's job is to make the book easier to read and harder to forget. The book's structure is the structure; the agent does not improve on it.

| I want to… | Use |
|---|---|
| Work through a book chapter by chapter, with primer, exercises and quiz | **this file** |
| Learn one large topic from many sources, in one narrative | `BLOG_STYLE_LEARNING.md` |
| Implement a book's algorithms myself, with the agent as lab assistant | `SELF_STUDY.md` |
| Capture one quick audit or comparison | `SINGLE_DOSSIER_THEME.md` |

**How to start.** Put the book file (PDF or EPUB) in a folder, hand an agent this file, and say *"build the book-guided dossier for <book>"*. The agent looks at the book, probes you, plans the whole book once, and then builds each chapter page as you reach it.

Reference implementation: none yet. When the first book is finished, record its path here and read it before starting the next.

---

## 0. What you are producing

A folder next to the book:

```
<book-folder>/
  <book>.pdf
  .alvar/                     # the reader's learning state, shared with the Alvar skills (§10)
    LEARNER.md
    maps/<book>-chNN.md       # one quiz map per chapter
  dossier/
    index.html                # the hub: how to use, chapters and status, systems index, glossary, references, revision log
    ch01.html … chNN.html     # one page per book chapter, built when the reader gets there
    capstone.html             # one spine through the whole book, built at the end (§11)
    assets/dossier.css        # shared stylesheet (§12.3)
    assets/dossier.js         # shared toggles, copy buttons, filters, diagram zoom (§12.4)
    labs/chNN-<slug>/         # starter files, only for exercises that need more than two files
    NOTES.md                  # the agent's working notes (§0.1)
    audit.py                  # §13
```

Each **chapter page** is two halves with the book in between:

| Part | Reader time | Words | Job |
|---|---|---|---|
| **Before you read** | ~10 min | ≤ 2,000 | The question the chapter answers, the words you will meet, one mental-model diagram, a reading guide, three questions to carry into the book, the pre-read quiz |
| **Read the book** | the book's time | one card | Pages, time, what to keep in mind |
| **After you read** | ~20 min | ≤ 4,000 | The carried questions answered, the chapter on one screen, 4–7 key learnings with worked examples, real systems, 2–3 exercises, questions with toggled answers, the lock-in quiz |

These budgets are defaults; the reader contract (§2) can move them. Reading time counts visible prose at 200 words per minute and leaves out Mermaid source, exercise hints and exercise solutions.

The **hub** is the prologue: it explains the sandwich once and tracks progress. The **capstone** is the epilogue: one object followed through every chapter, tying the per-chapter spines together.

You are writing what a friend who has read the chapter twice would hand you: a map before you go in, and afterwards, the parts worth keeping, made concrete and tested.

### 0.1 NOTES.md

The agent's persistence layer. Another agent must be able to resume from it alone.

````markdown
# NOTES — <Book>, <edition>

## Reader contract            (Phase 0 answers, dated, in the reader's words)
## Book facts                 (file, edition, printed-page offset, chapter list with pages, has exercises: yes/no)
## Layer tags                 (4–6, with colours)
## Connecting spine           (the capstone object, one line per chapter)
## Chapter status
| chapter | built | pre-read probe | lock-in | open edges |
## Systems index
| system | full card | back cards |
## Chapter inventories        (§3.2, one block per chapter)
## Budget
```json budget
{"before": 10, "after": 20}
```
## Terms                      (term → chapter that defines it; the audit reads this)
```json terms
{"replication log": "ch06", "snapshot isolation": "ch08"}
```
## Decisions log
````

---

## 1. The non-negotiables

Everything else is adjustable. These are not.

### 1.1 The book is the source of truth

- **Read the chapter from the file before writing a word about it.** Never summarise from memory. Editions differ, and model memory mixes them: DDIA's second edition calls its partitioning chapter *Sharding*, and a summary written from memory of the first edition will quietly teach the old book.
- **Every claim about the book carries a printed page reference** (`p. 303`, `pp. 209–214`). Use the printed page. Record the PDF-to-printed offset in `NOTES.md` once.
- Where practice or later work has moved on from the book, say so in a marked **"Since the book"** note, with a verified source and a date. Do not silently correct the author, and do not argue with them without a source.
- Paraphrase by default. Quote only when the author's exact words are the point: at most two sentences, with the page. Redraw figures as Mermaid and cite the original ("after the book's write-skew figure, p. 303").

### 1.2 Progressive vocabulary, across the whole book

**Never use a term of art before defining it.** The book's chapter order is the dependency order.

- First use in the dossier: **bold** the term and define it in plain language in the same sentence or the next. Expand every acronym on first use.
- A term defined in an earlier chapter may be used freely later. On its first use in a page, link it to the hub glossary (`index.html#g-<slug>`), so a reader who skipped ahead can catch up.
- **The book's forward references do not license yours.** When the book says "we will see this in Chapter 10", name the thing in plain words and the chapter by name: "a way for machines to agree on one value even when some of them fail, which is the consensus chapter". The later term stays out until its chapter.
- Real systems follow the same rule. Their jargon is vocabulary: a Kafka *topic* is defined before it is used.
- Two allowed exceptions: everyday engineering words the reader already owns (§2.2), and words inside the title of a cited reference.

### 1.3 Grounding

Every abstract claim is anchored within a sentence or two: a page reference, a command, a config line, a number, or a diagram callout. A paragraph with no anchor is decoration. Cut it or ground it.

### 1.4 Every question ships with its answer

Carried-in questions are answered in the after half. Book questions and check-yourself questions each have an answer toggle underneath. Exercises end in a solution toggle. §9 says what an answer must contain.

### 1.5 Verified references only

Verify every URL with web tools before it goes in a page. The book's own reference list is the first place to look (§4). If a URL cannot be verified, name the resource and how to find it. Never invent a title, author, date, or link.

### 1.6 Every real system starts from zero

Assume the reader has never used the systems you bring in. The first time a system appears anywhere in the dossier, it gets a full card that says what it is and why it exists before it says anything about the chapter (§7).

---

## 2. Phase 0 — Look, then probe the reader, then plan the book

Do not start writing after one prompt.

### 2.1 Look before you ask

Find out what the files can tell you, so the questions cover only what they cannot:

- **Book facts:** title, authors, edition, chapter list with printed page ranges (`pdfinfo`, then the table of contents through the Read tool), and the printed-page offset. Find chapter 1's first page; for DDIA's second edition, PDF page = printed page + 24.
- **Does the book have exercises?** Search the extracted text for "Exercises", "Problems", "Questions", "Homework" near chapter ends. Many textbooks have them (CLRS, the homework simulations in OSTEP). Many trade books do not: every DDIA chapter ends with a Summary and a reference list. Record the answer, because it switches §9's branches.
- **The preface.** Read it. It usually says who the book is for, which chapters depend on which, and which reading paths the authors suggest. That is free planning input.
- **The reader's files:** `.alvar/LEARNER.md` (if it exists, it already answers the style and starting-point questions; confirm it and ask only what it leaves open), existing `.alvar/maps/`, and an existing `dossier/NOTES.md` (resume from it; do not restart).
- **Tools:** which Alvar skills are installed (`probe`, `teach`, `learn-profile`, `learn-verify`, `learn-visual`), and whether Docker or the reader's language toolchain is present, if you can check.

### 2.2 The must-ask four

Use the harness question tool (`AskUserQuestion` in Claude Code). **Cap the first round at four questions.** Offer a "use your judgement" escape on each.

1. **Finish line.** "When you close the book, what should you be able to do or explain?" Push for something testable: "design the storage and replication layer for a service and defend each trade-off" beats "understand distributed systems". This sets the depth and decides which chapters get the heavier treatment.
2. **Starting point.** "What do you already know well, and what is new?" This sets the bootstrap vocabulary: the words you may use without ceremony. If `LEARNER.md` exists, confirm it instead of re-asking. If it does not, offer the `learn-profile` skill.
3. **Hands-on environment.** Operating system, Docker or not, preferred language, and how long one exercise may take. Every demo and exercise is written against this answer.
4. **Reading mode.** The default is the sandwich: dossier, then book, then dossier. The alternatives are book first with the dossier as consolidation only (drop the before half), or the dossier as a replacement for the book (merge the halves and raise the budget). §5 is built for the sandwich.

### 2.3 Ask if not obvious

- **Style, shown rather than described.** Write one short paragraph from the book's first chapter in three registers and offer them as options with previews: *plain and dense*, *plain with more examples*, *terse notes*. The reader picks by reading. Nobody can answer "what tone do you like?", but everyone can pick a paragraph.
- **Real-system bias.** "Any systems you use at work, or want to learn, that should be the examples?" Names the reader gives win ties in §7.1.
- **Budget.** Minutes before and after the book, if the defaults feel wrong.
- **Density and quoting.** Default: rich without being verbose; paraphrase first, short quotes, page references always. Ask only if the reader plans to publish.
- **Scope.** All chapters or a subset? In book order? Out-of-order reading works (glossary links carry it), but the vocabulary audit assumes book order.
- **Existing pain.** "Anything in this book you have tried before and bounced off?" That chapter gets an extra diagram and a slower primer, guaranteed.

Record the answers in `NOTES.md` under **Reader contract**, dated, in the reader's words. Offer to write the style and starting-point answers into `.alvar/LEARNER.md`, so the Alvar skills fit the same person.

### 2.4 Present the book plan and stop

Before building any page, present one compact plan and wait for approval:

- the folder, the hub, and the build order (chapter by chapter, as the reader reaches it)
- a table: chapter | title | printed pages | **per-chapter spine** | **terms introduced** | real systems (full card or back card) | exercise ideas | book questions (yes/no)
- **the connecting spine** for the capstone, in one sentence, plus one line per chapter on what that chapter does to it (§11)
- 4–6 layer tags for the book (§4)
- the cut lever: "if a chapter runs over budget, I trim worked examples and the second system card; I never trim the vocabulary discipline, the page references, the diagrams, or the answers"
- explicit assumptions

The terms column is where vocabulary violations are cheapest to catch. The book's order does most of the work, but system cards and exercises pull terms forward; check them here.

### 2.5 Per chapter: a mini-plan

Before each chapter page, re-read that chapter's row and post a mini-plan of at most ten lines: spine, strands (§3.3), terms, diagrams, systems, exercises. **Wait for approval on the first chapter.** After that, post the mini-plan and continue unless the reader asked to review each one. Ask at most two questions per chapter, and only at a real fork: "the transactions chapter can anchor on PostgreSQL or MySQL; which do you use?"

---

## 3. Phase 1 — Read the chapter and build its inventory

### 3.1 Extraction

- **Text:** `pdftotext -f <first> -l <last> -layout book.pdf -` (these are PDF page numbers). For EPUB, `pandoc book.epub -t plain`, or unzip it and read the XHTML. For a scanned PDF where extraction returns nothing, read the pages visually.
- **Figures, tables, and anything you quote:** open those pages with the Read tool and look at them. Extracted text garbles hyphenation and ligatures; DDIA's extraction turns a line-broken "Chapter" into "Chap!ter". Never copy a quote from extracted text without checking the rendered page.
- Read the **whole** chapter, including its summary and reference list, before planning the page.

### 3.2 The inventory

Write it into `NOTES.md` under the chapter before writing any HTML. It is the working surface for the page and for the audit.

- **Sections** with printed page ranges (these become the reading guide).
- **Terms** the chapter introduces, each with the page where the book defines it.
- **Core claims:** 5–10 sentences, each with a page.
- **The book's examples and case studies,** with pages. Reuse them; the reader has just read them.
- **Figures worth redrawing.**
- **The book's questions or exercises,** if any, with pages.
- **Forward references** the book makes, so they do not become early vocabulary in the dossier.
- **References worth reading** from the chapter's reference list.

### 3.3 Strands

From the claims, pick **4–7 strands**: the ideas someone must hold to say they understood the chapter. Name each one in words and give it a slug (`leader-follower`, `replication-lag`). The strands are the chapter's backbone, and they are used four times:

1. one key-learning section each in the after half (§5.3)
2. the reading guide's "what to look for" column
3. the strand list in both quiz cards (§10)
4. the rows of the reader's `.alvar/maps/<book>-chNN.md`

If a strand cannot be quizzed, it is a topic. Sharpen it into a claim.

### 3.4 The per-chapter spine

Pick one concrete object or event that the chapter's ideas act on, preferably one the book itself uses, and follow it through the page. The spine sentence goes in the metadata line, and each key learning moves the object one step.

| Book · chapter | Per-chapter spine |
|---|---|
| DDIA · Storage and retrieval | the life of one key-value write, from append to compaction |
| DDIA · Replication | one write on its way to three replicas |
| DDIA · Transactions | two on-call doctors going off call at the same moment (the book's write-skew example, p. 303) |
| DDIA · Batch processing | one nginx access log becoming a list of the five most popular pages (the book's example, p. 454) |
| OSTEP · Paging | one virtual address becoming a physical one |
| Kurose and Ross · Transport layer | one TCP segment, lost and resent |

The chapter-level spine is what turns a page of notes into one explanation. The book-level spine is separate and appears only in the capstone (§11).

---

## 4. Phase 2 — Verify references and real systems

Do this per chapter, in parallel, before writing the after half.

- **Start from the book's reference list.** Many books cite with DOIs and archive links; DDIA uses `doi:` identifiers and `perma.cc` archives. Resolve DOIs through `https://doi.org/<doi>`, check the archive link, and prefer the author's open copy when one exists. Pick 2–5 per chapter and annotate *why and when* to read each: "read after the replication-lag learning; the diagrams are the value".
- **Then primary sources for the real systems:** official documentation for every demo command, image name, and config key. If an environment is available, **run the demo** and paste the real output. If not, label the transcript illustrative.
- **Date anything volatile** ("as of September 2026"). Versions, image tags, defaults and product names drift; the book's ideas mostly do not. The stable-versus-drifting line on each system card (§7.2) is where that split lives.
- **Layer tags:** 4–6 per book, used on chapters, references and systems. DDIA might use `MODEL` `STORAGE` `DIST` `TXN` `STREAM` `OPS`; an operating-systems book might use `CPU` `MEMORY` `CONCURRENCY` `PERSISTENCE`.

Prefer, in order: official documentation, maintainer-written material, the book's own references, upstream design docs, good third-party explainers. Reject SEO blog spam, content farms, and anything undated or contradicting a primary source.

---

## 5. Phase 3 — Chapter page anatomy

In this order. Section ids are fixed, because the audit (§13) and the cross-page links rely on them. Sections never nest, and the `id` attribute comes first in each `<section>` tag.

### 5.1 Before you read — `<section id="before">`, ~10 min

1. **Chapter label, title, metadata line:** `~10 min before · pp. 197–250 (~2 h) · ~20 min after · [TAG]`, then the **spine sentence** in `<span class="spine">`.
2. **The question this chapter answers.** One or two paragraphs that set up the tension: what goes wrong without the ideas in this chapter. Ground it in the spine.
3. **Words you will meet** (`.words`). The chapter's new terms, 6–12, each defined in one plain line. This is the chapter's word list. It sits at the front because the reader meets these terms in the book next. The after half deepens them without redefining them.
4. **The mental model.** One diagram of the shape the chapter hangs on, with a caption that says what to notice.
5. **Reading guide** (`<table class="guide" id="guide">`): book section | pages | how to read (`close`, `skim`, `later`) | what to look for, which is a strand in words. Say what to skip and why: "the history of the format, pp. 164–165: interesting, and nothing later depends on it." Readers relax when the scope is fenced.
6. **Carry these into the book** (`.carry`): exactly three questions the chapter answers, phrased so the reader notices the answer when it arrives. They are answered in the after half.
7. **Pre-read quiz card** (`.quiz data-when="before"`, §10).

Keep the primer a primer. It gives vocabulary, shape and questions, and leaves the mechanisms to the book, which teaches them better. The test: after the primer the reader can parse every sentence of the chapter, is curious about every strand, and cannot yet answer the carried questions.

### 5.2 Read the book — `<section id="book">`

One card: "Now read pages 197–250, about two hours. Keep the three questions in mind and note the page where each one gets answered. Then come back for the after half." Nothing else. This card is what makes the sandwich visible on the page.

### 5.3 After you read — `<section id="after">`, ~20 min

1. **The questions you carried in** (`.carried`): the three questions, each with an answer toggle that names the page where the book answers it. It comes first, so the loop from the primer closes at once.
2. **The chapter on one screen:** 250–400 words, the chapter's argument in order, with page references inline. Write it so it still works as a revision summary a month later.
3. **Key learnings:** one `<h3 class="strand" id="s-<slug>">` per strand, 250–450 words each. Each one goes: the claim in plain words → a worked example on the spine (numbers, a trace, a config, a command) → the page reference → where it matters in practice. At least one diagram across this block. "Since the book" notes (§1.1) go here.
4. **Meet the system** (§7): one or two cards, full or back.
5. **Hands-on** (§8): two or three exercises, each with a hint ladder and a solution.
6. **From the book** (`.bookq`, only if the book has questions), then **Check yourself** (`.check`), per §9.
7. **Recap** (`.recap`): 3–5 dense sentences, no new information.
8. **Go deeper** (`.refs`): 2–5 verified references, layer-tagged, each with why and when.
9. **Lock-in quiz card** (`.quiz data-when="after"`, §10).

After `</section>` of the after half, and only after a quiz round, comes the dated **revision card** (§10.5).

A term that first appears in the after half (this should be rare) goes into the before half's word list as well, marked "(after reading)".

### 5.4 Budget and the cut lever

| Block | Words |
|---|---|
| Before half, total | ≤ 2,000 |
| Carried answers | 3 × 60–120 |
| The chapter on one screen | 250–400 |
| Key learnings | 4–7 × 250–450 |
| Meet the system (prose, excluding code) | 1–2 × 200–400 |
| Exercises (the visible part) | 2–3 × 80–150 |
| Questions with answers | 5–7 × 60–170 |
| Recap | 60–120 |

If the after half runs over, cut in this order: the second system card shrinks to a back card or goes; the key learning with the least new mechanism merges into its neighbour; worked examples get shorter. Page references, answers, diagrams and vocabulary discipline are never cut.

---

## 6. Writing rules

The same voice as the parent spec, restated in full, because a book dossier drifts toward the book's register, which is usually more formal than this one.

### 6.1 Voice

- Direct address or first-person-plural. "Let us be concrete about the danger." "You have seen it."
- **Simple, spoken English.** Short sentences. Assume a non-native reader with strong technical skills. Prefer the plain word: *use* over *utilise*, *stop* over *cease*, *build* over *construct*.
- Short paragraphs, 2 to 5 sentences. White space is a feature.
- Occasional dry humour, never breathless.
- Prose first. Bullet lists only for genuinely enumerable things: commands, checklists, knob tables.
- **Attribute, then explain.** When a claim is the book's, name the book ("the authors argue, p. 402"). When explaining, speak as yourself.

**Banned words, group one** (they read as machine-written): *leverage, delve, seamless, robust, cutting-edge, in today's world, it's important to note, smoking gun, unlock, supercharge, game-changer, journey, dive deep, at the end of the day, comprehensive, cornerstone.*

**Banned words, group two: metaphors where a literal word exists.**

| Do not write | Write |
|---|---|
| leak (as a metaphor) | the literal thing that happens |
| load bearing | this matters because … |
| survives | still true, still there |
| edge, sharp edge | problem, trap |
| hurt, bites | the literal failure |
| under the hood | inside, in the implementation |

*(`edge` as an Alvar status value in §10 is a data label, not prose.)*

**Banned sentence shapes:**

- **No "X, not Y" contrasts.** State what it is, once.
- **No negative listing** ("not X, not Y, not Z"). Say what is there.
- **No metaphor nouns** as the subject of a claim. State the fact.
- **No em-dash-heavy prose.** A dash in a definition list or a reference line is fine; a paragraph with three is not. Use a full stop.
- No three-adjective lists, no sentence that restates the previous one, no paragraph that only announces the next one.
- **Objects are "it".**

### 6.2 Techniques

- **Say what you are skipping, and why.** The reading guide does this for the book; do it for your own sections too.
- **Repeat the key fact in three registers:** prose, diagram, one-line recap.
- **Reuse the book's example before inventing one.** The reader has just read it, so building on it costs nothing. Invent a new example only when the book's is abstract or a number would make it land.
- **Point back mechanically.** Make an earlier chapter's fact do visible work: "this is the replication log from the replication chapter, now read by a stream consumer". Across separate pages this is what keeps the dossier one document.
- **The absence list.** When something is fast or safe because of what it skips, list the skipped steps.
- **Knob tables.** Four or more settings get a table: knob → what it actually does.
- **Analogies: one per concept, then drop them.** The test: remove the analogy. If a mechanism got harder to understand, keep it. If the page only got less lively, cut it.

### 6.3 Name things, never index them

Never refer to anything by a bare number or code: "chapter 7", "strand 3", "Q4" and "§5" are all unreadable to someone who does not hold your index. Name it: "the sharding chapter (chapter 7)", "the replication-lag learning". A number after the name is a fine pointer; a number alone is not. Printed page references (`p. 212`) are citations and are fine. This covers prose, diagram labels, captions, answers, quiz prompts, and anything you say in chat about the dossier.

### 6.4 Depth guardrail

Describe structures functionally. Show the shortest realistic command or config that proves the point, and label illustrative output as illustrative. If you find yourself transcribing the book, you have gone too deep; the reader has the book open.

---

## 7. Real-world systems: the newcomer rule

### 7.1 Choosing systems

- The chapter's mechanism must be **visible** in the system: a command shows it, a config names it, a log line prints it.
- It must **run on the reader's machine**, in the environment from §2.2, within minutes.
- Prefer, in order: systems the reader named, systems the book names, the most widely deployed option.
- **Spread them out.** Each system anchors one or two chapters and appears elsewhere as a back card. The same system in every chapter teaches the system and loses the book.
- A chapter with no natural system gets none. An ethics chapter needs a case study, and a container would add nothing.

### 7.2 The full card (first appearance in the dossier)

```html
<div class="system" id="sys-kafka" data-system="kafka">
  <h4>Meet the system · Apache Kafka</h4>
  <p><b>What it is.</b> Two sentences a newcomer can repeat to someone else.</p>
  <p><b>Why it exists.</b> The problem that made someone build it, in 2–3 sentences; who built it and when, if that helps.</p>
  <p><b>The parts you need.</b></p>
  <ul><li>At most four components, each defined in one line.</li></ul>
  <p class="here"><b>Where this chapter lives inside it.</b> The mapping from the chapter's strands to the system's parts. This paragraph is why the card exists.</p>
  <p><b>Try it</b> (~5 min, needs Docker):</p>
  <pre><code>…at most ten lines…</code></pre>
  <p><b>What you should see.</b></p>
  <pre><code>…real output, or labelled illustrative…</code></pre>
  <p><b>Clean up.</b> <code>docker rm -f …</code></p>
  <p class="drift">Stable: the ideas above. Drifting (as of MONTH YEAR): image tags, default settings, CLI flags.</p>
</div>
```

The "where this chapter lives" paragraph is the one that must be excellent. A card that describes the system well and never connects it to the chapter is a product page.

### 7.3 The back card (every later appearance)

Two to four sentences: what the system is, in one line; which part of it this chapter explains; a link to the full card. Optionally one more command.

```html
<div class="system back" id="sys-kafka-back" data-system="kafka">
  <h4>Back to Apache Kafka</h4>
  <p>… <a href="ch12.html#sys-kafka">the full card in the stream-processing chapter</a>.</p>
</div>
```

### 7.4 The systems index

The hub keeps one table: system | what it is (one line) | full card | back cards. Each row carries `id="systems-<slug>"`. Update it with every chapter, and mirror it in `NOTES.md`.

---

## 8. Hands-on exercises: the hint ladder

### 8.1 Feasibility

- Runs on the reader's machine, in the §2.2 environment, in **20–60 minutes**. Label the time.
- **Stands alone.** No exercise depends on an earlier chapter's lab state. Each one starts from nothing, or from a starter block in the page.
- **At least one exercise per chapter needs no environment:** trace it on paper, compute it by hand, predict and then check.
- **The acceptance check comes first.** "Done when" tells the reader what they will see when they are right, before they start.
- **Run the solution before it ships** when an environment is available. If not, the solution says "not executed" plainly.

### 8.2 Kinds

Pick two or three different kinds per chapter.

| Kind | Shape | Example |
|---|---|---|
| **Build a tiny one** | 30–80 lines that implement the chapter's core mechanism | an append-only key-value store with an in-memory hash index, then compaction |
| **Measure** | run something and compute the number the chapter talks about | p50, p95 and p99 from a file of request latencies |
| **Break a real one** | provoke the failure the chapter describes, in a real system | two `psql` sessions reproducing the on-call doctors' write skew under repeatable read, then fixing it with serializable |
| **Trace by hand** | pen and paper, predict each step | Lamport timestamps across three processes |
| **Read the source** | find the mechanism in a real codebase and explain it | find where a small LSM-tree library decides to compact |

### 8.3 Anatomy and markup

```html
<div class="lab" id="lab-hash-index">
  <h4>Hands-on · Build a log with a hash index</h4>
  <p class="labmeta">~40 min · build · needs Python 3</p>
  <p><b>Task.</b> What to build or do, in 2–4 sentences, tied to a named key learning.</p>
  <p><b>Done when.</b> The observable result.</p>
  <details class="hint"><summary>Hint 1 · direction</summary><p>Which idea from the chapter to reach for. No code.</p></details>
  <details class="hint"><summary>Hint 2 · mechanism</summary><p>The data structure, or the order of steps. A signature at most.</p></details>
  <details class="hint"><summary>Hint 3 · nearly there</summary><p>The one tricky part, with a fragment of code.</p></details>
  <details class="sol"><summary>Solution</summary>
    <pre><code>…</code></pre>
    <p><b>Expected output.</b> …</p>
    <p><b>What this shows.</b> Back to the key learning, by name, with its page.</p>
    <p><b>Common wrong turn.</b> The mistake most people make here, and why it fails.</p>
  </details>
</div>
```

The hints climb from direction to mechanism to nearly-code. Each is useful on its own, and the reader opens only as many as they need. The "common wrong turn" does the job the near-miss does in an answer, and it is usually the most valuable line in the lab.

Code in the page must be copy-runnable. An exercise that needs more than two files puts its **starter** files in `labs/chNN-<slug>/` and links them. Solutions stay in the toggle and never go on disk, so opening one stays a deliberate choice.

**No `<div>` inside a lab, a system card, a quiz card, or a question block.** The audit depends on it, and figures belong outside cards anyway.

---

## 9. Questions and answers

### 9.1 Which questions

- **The book has questions or exercises:** each one gets an entry in `.bookq`, in the book's order, with its page. Quote the stem if it is short (two sentences at most); otherwise paraphrase it. Each gets an answer toggle. For open-ended or proof questions, the answer gives the approach, the key step, and what a complete answer must contain. If the chapter has more than eight, answer the eight that best test the strands and list the rest by page so the reader can find them. Then add 3–5 of your own in `.check`, aimed at whatever the book's set leaves untested, which is usually diagnosis.
- **The book has none** (DDIA): write 5–7 in `.check`.

### 9.2 A mix of framings is required

Across the book questions and check-yourself questions, each chapter needs:

- at least one **predict the value** (a number, with the arithmetic in the answer)
- at least one **here is a symptom, what happened?** (a log line, a stale read, a slow query; the reader names the mechanism)
- at least one **choose and defend** (two designs, one situation: pick one and give the trade-off)
- at most two **recall** questions

The diagnostic framing matters most. Quiz rounds on the parent spec's documents found that readers answered "what is true?" questions well, then answered "here is a symptom" questions by inventing a component that does not exist. Only the second framing catches that.

### 9.3 What an answer contains

In this order:

1. The answer itself, flat, in the first clause. No throat-clearing.
2. The mechanism, why, in one or two sentences.
3. Where to check it: the **book page**, a command, a field name, or the diagram.
4. For "predict the value": the number **and** the arithmetic that produced it.
5. The near-miss, when there is an obvious one: the plausible wrong answer, and what is wrong with it. This is usually the highest-value sentence, because it is the one the reader could not have written themselves.

40–150 words. An answer longer than the paragraph that taught it means the page is missing something; fix the page. Do not restate the question in the affirmative, and do not write "as covered above". If the honest answer is only a pointer, the question was trivia; replace it.

**Check each answer against the book, not against your own summary.** An answer written from memory of your own prose is how a loose claim reaches the quiz, where it does the most damage.

### 9.4 Markup

```html
<div class="check" id="questions">
  <h4>Check yourself</h4>
  <p class="note"><button id="answerctl">Open all answers</button> Answer out loud first, then open.</p>
  <p>1. Predict the value: …</p>
  <details class="ans"><summary>Answer</summary><p>…</p></details>
  <p>2. Here is a symptom: …</p>
  <details class="ans"><summary>Answer</summary><p>…</p></details>
</div>
```

`.bookq` and `.carried` use the same shape: `<p>1. The question. <span class="pageref">p. 212</span></p>`, then one `<details class="ans">`. Each question paragraph is a bare `<p>` that starts with its number, which is how the audit counts them. The toggle is native `<details>`, collapsed by default, so answering first is the path of least resistance and opening is a deliberate click. The reveal-all button and the print hook live in `assets/dossier.js` (§12.4). The print hook opens every toggle, because a collapsed `<details>` does not print.

---

## 10. The Alvar quiz, per chapter

The toggled answers let a reader check themselves. That is necessary and not sufficient: **nobody catches themselves being vague.** A reader who half-knows an answer opens the toggle, recognises it, and files the feeling as knowledge. So each chapter carries two quiz cards that start a real quiz with an agent, one on each side of the book.

### 10.1 The two quizzes

| | Pre-read probe | Lock-in quiz |
|---|---|---|
| **When** | after the before half, before the book | after the after half and the exercises |
| **Size** | 3–5 questions, broad | every strand at least once, 1–3 questions per call |
| **Purpose** | find what the reader already holds, and upgrade the reading guide: `unknown` strands become "read closely" | measure what locked, and send `edge` and `unknown` strands to `teach` |
| **Expected result** | plenty of `unknown` and `blocked`, which is fine | mostly `known`; every miss becomes a page fix |
| **Writes** | `.alvar/maps/<book>-chNN.md` | the same map, with the pre-read round kept in the log |

### 10.2 The quiz card

Same strands, same map file, two prompts. The prompt text is the guide: a reader who has never heard of Alvar can paste it and get the right quiz.

```html
<div class="quiz" data-when="before" id="quiz-before">
  <h4>Quiz · before you read</h4>
  <p>This chapter's strands:</p>
  <ul class="strands">
    <li data-strand="leader-follower">Leaders and followers</li>
    <li data-strand="replication-lag">Replication lag, and what readers see</li>
  </ul>
  <p>Paste this to an agent that has the Alvar skills (<code>probe</code>, <code>teach</code>):</p>
  <pre class="prompt"><code>…the pre-read prompt below…</code></pre>
  <button class="copy">Copy prompt</button>
  <p class="note">known: right, with a sound reason · edge: right but thin, or a near miss · unknown: the foundation is missing · blocked: "I don't know".</p>
</div>
```

The pre-read prompt:

```
/probe Book: BOOK (EDITION), chapter N "TITLE", printed pp. A–B.
I have NOT read it yet. Find what I already know, so I know where to read closely.
Strands: leader-follower (Leaders and followers), replication-lag (Replication lag, and what readers see), …
3–5 questions, broad first. Use the question tool, never A/B/C/D in chat. "I don't know" is a fine answer here.
Write the map to .alvar/maps/SLUG-chNN.md.
End with a reading plan: for each row of the reading guide in dossier/chNN.html, say "read closely" or "skim".
```

The lock-in prompt (the card with `data-when="after"`, `id="quiz-after"`):

```
/probe Book: BOOK (EDITION), chapter N "TITLE", printed pp. A–B.
I have read the chapter and the after half of dossier/chNN.html. Lock in.
Strands: … (the same list)
Quiz every strand at least once, and include at least one "here is a symptom, what happened?" question.
1–3 questions per call. Score my reasoning, not the letter. One-line correction after each batch.
Update .alvar/maps/SLUG-chNN.md and keep the pre-read round in the quiz log.
Then offer /teach on every strand still edge or unknown, one reasoning step at a time.
Finally, list the claims in dossier/chNN.html that the quiz showed were loose, so the page can get a revision card.
```

Without the Alvar skills installed, the reader pastes the same prompt without `/probe` to any agent that has this file. §10.3 is the same protocol.

### 10.3 The protocol, inline

Use the skills when they are installed: `probe` to measure, `teach` to close what the probe found, `learn-verify` to check a claim before it is taught as fact, `learn-visual` for a diagram of one idea. They carry the scoring vocabulary and the file layout, and a second agent can pick the work up from what they leave. Without them, follow this.

**Never print A/B/C/D in the chat.** Pasted multiple choice lets the learner scan all options at once and pattern-match, and it gives no structured result to score. Call the harness question tool:

| If the harness has | Call |
|---|---|
| `AskUserQuestion` | that (Claude Code) |
| `ask_user_question` | that (Grok Build, Codex) |
| `question` | that (OpenCode) |
| `quiz`, else `ask_user` / `askUserQuestion` | that (Pi) |

No match: say which tool is missing and stop. Do not fall back to pasted letters.

- **1–3 questions per call, then wait.**
- **One right answer**, single-select: three content choices plus **"I don't know"**, always.
- **Do not mark the correct option "recommended", and do not habitually put it first.** Either one leaks the answer. Shuffle the position.
- Distractors are wrong for a reason, never by wording. The best one encodes the misconception you expect: the component that does not exist, the check that does not happen.
- **Invite a talk-through** in the free-text slot. Reasoning is what you are measuring.
- Start wide, then split whichever strand the answer left ambiguous. Skip strands the reader has already shown.

| Result | Status |
|---|---|
| correct, with a sound reason | `known` |
| correct, thin or absent reason | `edge` |
| wrong, but a near miss | `edge` |
| wrong, foundation missing | `unknown` |
| "I don't know" | `blocked` |

A right letter with a wrong reason is `edge`. That distinction is the whole value of the exercise. After each batch, give a one-line correction for each miss; teaching comes after measurement.

### 10.4 The map file

`.alvar/maps/<book>-chNN.md` holds the goal, a strand table (`strand | status | evidence`), and a quiz log (`Q1 [strand] <choice> — correct|wrong|idk — five words`), with the pre-read and lock-in rounds labelled. After each round, update the chapter's status on the hub (§12.2) and in `NOTES.md` from the map.

### 10.5 Fix the page, not the reader

A wrong or hesitant lock-in answer is a defect in the page, or in how the page points into the book.

- If the reader invented a component, the page described an outcome without naming the mechanism that produces it. Name the mechanism.
- If they got *who* right and *how* wrong, the page said who and skipped how. That is the commonest gap this format produces.
- **Re-read the book's pages before writing the correction.** Several corrections turn out sharper than what the page said, and that sharpening belongs in the page.

Then add a dated **revision card** after the after half: `<section id="revision-YYYY-MM-DD" class="card revision">`, 200–400 words, in two parts.

1. **Which claims the quiz sharpened**, one line each, naming the key learning, each marked *wrong* or *true-but-loose*. Almost all are the second kind.
2. **The shape the misses share**, written about the page and never about the reader ("this page taught who owns the log and skipped how followers catch up"), plus one sentence the reader can carry. Group misses by framing (recall versus diagnosis), not by learning. No scores, no names.

If a correction changes what a learning teaches, fix the learning too and let the card point at it. Add a one-line entry to the hub's revision log. The audit skips `revision-*` sections for vocabulary, because the card is written for someone who has read the whole chapter.

---

## 11. The capstone: one spine through the whole book

Each chapter has its own spine. The capstone ties them together at the end with **one** object followed through every chapter.

- **Choose it at book-plan time** (§2.4), so you can check that every chapter has something to do to it. It does not appear on chapter pages; the chapters keep their own spines.
- **Build it after the last chapter,** or when the reader asks for it.
- **One stop per chapter:** what happens to the object at this stop, which key learning acts on it (named and linked), and a pointer back to that chapter's own spine: "the replication chapter followed one write to three replicas; our comment is that write".
- **Two diagrams:** a full-book tower (the layers the object passes through, reusing box names from the chapter diagrams) and an end-to-end sequence diagram of the object's whole life.
- **The payoff sentence:** once, in bold, placed where every word in it has been defined. It is the sentence the whole book exists to let you say.
- **A cross-book quiz card:** a lock-in prompt that draws strands from several chapters, diagnostic framing only: "here is a symptom in production; which chapter's mechanism failed?"

DDIA example: *the life of one post on a social network*, extending the book's own home-timeline case study (pp. 34–37). Which kind of system receives it, how the latency of its timeline is measured, how it is modelled, encoded, stored, replicated, sharded and committed, what happens to it when the network fails, how the replicas agree on it, how a batch job and a stream processor derive views from it, and whose data it is.

---

## 12. Phase 4 — The HTML build

A folder of static pages. No build step. Shared CSS and JS in `assets/`; everything else inline except the Mermaid CDN import. The look is the parent spec's (dark blue hero, white cards, Mermaid), extended with the components below. Do not mix in the `SINGLE_DOSSIER_THEME.md` look.

### 12.1 Chapter page skeleton

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>TITLE · chapter N · BOOK dossier</title>
<link rel="stylesheet" href="assets/dossier.css">
</head>
<body>
<header class="hero hero-ch">
  <div class="hero-in">
    <p class="crumbs"><a href="index.html">BOOK</a> · chapter N of M</p>
    <h1>TITLE</h1>
    <p class="sub">What you can explain after this chapter, in one sentence.</p>
  </div>
</header>
<div class="wrap">
  <nav id="toc">
    <h2>Before you read</h2>
    <ol><li><a href="#question">The question</a></li><li><a href="#words">Words you will meet</a></li>
        <li><a href="#model">The mental model</a></li><li><a href="#guide">Reading guide</a></li>
        <li><a href="#carry">Carry these in</a></li><li><a href="#quiz-before">Pre-read quiz</a></li></ol>
    <h2>The book</h2>
    <ol><li><a href="#book">pp. A–B</a></li></ol>
    <h2>After you read</h2>
    <ol><li><a href="#carried">Your questions, answered</a></li><li><a href="#onescreen">The chapter on one screen</a></li>
        <li><a href="#s-SLUG">…one entry per key learning…</a></li>
        <li><a href="#systems">Real systems</a></li><li><a href="#labs">Hands-on</a></li>
        <li><a href="#questions">Check yourself</a></li><li><a href="#quiz-after">Lock-in quiz</a></li></ol>
  </nav>
  <main>
    <section id="before" class="chapter">
      <span class="chnum">Chapter N · before you read</span>
      <h2>TITLE</h2>
      <p class="chmeta">~10 min before · pp. A–B (~2 h) · ~20 min after · <span class="tag MODEL">MODEL</span><br>
        <span class="spine">The spine sentence.</span></p>
      <h3 id="question">The question this chapter answers</h3>
      …
      <div class="words" id="words"><h4>Words you will meet</h4><dl><dt id="w-SLUG">term</dt><dd>…</dd></dl></div>
      <h3 id="model">The mental model</h3>
      <figure class="dia"><div class="mermaid">
flowchart LR
  …
</div><figcaption><b>Diagram N.1 — what it is.</b> Notice …</figcaption></figure>
      <h3>Reading guide</h3>
      <table class="guide" id="guide">
        <tr><th>Book section</th><th>Pages</th><th>How</th><th>Look for</th></tr>
        <tr><td>…</td><td>pp. …</td><td><span class="how close">close</span></td><td>…</td></tr>
      </table>
      <div class="carry" id="carry"><h4>Carry these into the book</h4><ol><li>…</li><li>…</li><li>…</li></ol></div>
      <div class="quiz" data-when="before" id="quiz-before">…</div>
    </section>
    <section id="book" class="card readnow">
      <h2>Now read the book</h2>
      <p>Pages A–B, about two hours. Keep the three questions in mind and note the page where each one is answered. Then come back.</p>
    </section>
    <section id="after" class="chapter">
      <span class="chnum">Chapter N · after you read</span>
      <div class="carried" id="carried"><h4>The questions you carried in</h4>…</div>
      <h3 id="onescreen">The chapter on one screen</h3>
      …
      <h3 class="strand" id="s-SLUG">Key learning, named</h3>
      …
      <!--APPEND-->
    </section>
  </main>
</div>
<nav class="chnav"><a href="chPREV.html">← PREVIOUS TITLE</a><a href="index.html">All chapters</a><a href="chNEXT.html">NEXT TITLE →</a></nav>
<footer>BOOK by AUTHORS, EDITION. Page references are to the printed edition. Built MONTH YEAR.</footer>
<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
  mermaid.initialize({startOnLoad:true, deterministicIds:true, theme:'neutral', flowchart:{htmlLabels:true, curve:'basis'}, securityLevel:'loose'});
</script>
<script src="assets/dossier.js"></script>
</body>
</html>
```

The first page has no previous link, and the last page links to the capstone once it exists. Open the systems block with `<h3 id="systems">Real systems</h3>` and the exercises with `<h3 id="labs">Hands-on</h3>`, so the table of contents resolves. The cards keep their own ids (`sys-<slug>`, `lab-<slug>`); an element carries one id only.

### 12.2 Hub skeleton

```html
<header class="hero">
  <div class="hero-in">
    <h1>BOOK TITLE</h1>
    <p class="sub">The reader's finish line, as one sentence.</p>
    <p class="meta">AUTHORS · EDITION · N chapters · K built · started MONTH YEAR</p>
  </div>
</header>
<div class="wrap">
  <nav id="toc"><h2>This dossier</h2><ol>
    <li><a href="#how-to-read">How to use it</a></li><li><a href="#chapters">Chapters</a></li>
    <li><a href="#systems">Real systems</a></li><li><a href="#glossary">Glossary</a></li>
    <li><a href="#references">References</a></li><li><a href="#revisions">Revision log</a></li></ol></nav>
  <main>
    <section id="how-to-read" class="card">…the text below…</section>
    <section id="chapters" class="card">
      <h2>Chapters</h2>
      <table class="chapters">
        <tr><th>Chapter</th><th>Pages</th><th>Before · after</th><th>Spine</th><th>Status</th></tr>
        <tr><td><a href="ch01.html">TITLE</a></td><td>pp. 1–32</td><td>10 · 20 min</td><td>…</td><td><span class="status locked">locked</span></td></tr>
        <tr><td>TITLE</td><td>pp. 33–64</td><td></td><td></td><td><span class="status">not built</span></td></tr>
      </table>
    </section>
    <section id="systems" class="card">
      <h2>Real systems</h2>
      <table><tr><th>System</th><th>What it is</th><th>Full card</th><th>Back cards</th></tr>
        <tr id="systems-kafka"><td>Apache Kafka</td><td>…</td><td><a href="ch12.html#sys-kafka">stream processing</a></td><td>…</td></tr></table>
    </section>
    <section id="glossary" class="card">
      <h2>Glossary</h2>
      <h3>TITLE OF CHAPTER</h3>
      <div class="words"><dl><dt id="g-SLUG">term</dt><dd>definition <a href="ch06.html#words">in the chapter</a></dd></dl></div>
    </section>
    <section id="references" class="card">
      <h2>References</h2>
      <p id="filters"><button data-l="MODEL">MODEL</button> …</p>
      <ul class="refs"><li class="ref" data-layer="MODEL"><span class="tag MODEL">MODEL</span> …</li></ul>
    </section>
    <section id="revisions" class="card"><h2>Revision log</h2><ul><li>YYYY-MM-DD · chapter name · what changed</li></ul></section>
  </main>
</div>
```

Chapter status values, updated from the maps: `not built` → `built` → `probed` (pre-read done) → `edges n` (lock-in done, n strands not yet `known`) → `locked` (every strand `known`).

**The "How to use it" card.** It travels with the folder, so it must work for a reader who is not the person who commissioned it. Adapt this wording:

> **How each chapter works.** Every chapter page has two halves, with the book between them.
> 1. **Before you read** (about 10 minutes): the question the chapter answers, the words you will meet, one diagram, and a reading guide that says which sections to read closely and which to skim.
> 2. **Pre-read quiz** (about 5 minutes): paste the prompt from the card at the end of that half into an agent. It finds what you already know and tells you where to slow down.
> 3. **The book.** Read the chapter with the three carried questions in mind.
> 4. **After you read** (about 20 minutes): your three questions answered, the chapter on one screen, the key ideas worked through with examples, real systems you can run on your laptop, and two or three exercises.
> 5. **Lock-in quiz.** Paste the second prompt. Whatever it finds loose goes back into the page as a dated revision card.
>
> **Words.** Every bold word is defined where it first appears and never used before that. Words from earlier chapters link to the glossary on this page.
>
> **Diagrams.** Every diagram has a *zoom* button in its corner. It opens the picture full screen: scroll to zoom, drag to pan, double-click to fit, Esc to close.
>
> **Check yourself.** Every question has its answer behind a toggle. Answer out loud first, in full sentences, and only then open it. A question you can half-answer is the one worth re-reading for. Exercises climb through hints before the solution; open only as many as you need.
>
> **Getting quizzed properly.** The quiz cards are written for an agent running the Alvar `probe` and `teach` skills. It asks through a real question tool, scores your reasoning rather than your letter, and writes what it found to `.alvar/maps/`. Without the skills, paste the same prompt to any agent along with `BOOK_GUIDED_BLOG_STYLE_LEARNING.md`.

### 12.3 Stylesheet — `assets/dossier.css`

Copy this wholesale. The first block is the parent spec's tuned stylesheet with generic layer-tag names; the second holds the book-guided components. Rename the six `.tag` classes to the book's layer tags.

```css
:root{
  --ink:#1b1f24; --ink-soft:#4a5560; --line:#dfe4ea; --bg:#fbfcfd; --panel:#fff;
  --accent:#0b6bcb; --accent-soft:#e8f1fb; --warm:#b45309; --warm-soft:#fdf3e3;
  --good:#0f7b6c; --good-soft:#e6f4f1; --code-bg:#f4f6f8;
  --sys:#7c3aed; --sys-soft:#f3eefe; --book:#be123c; --book-soft:#fdeef1;
  /* one colour per layer tag; rename the classes at the bottom of this block */
  --t1:#7c3aed; --t2:#0b6bcb; --t3:#0f7b6c; --t4:#b45309; --t5:#be123c; --t6:#475569;
}
*{box-sizing:border-box}
html{scroll-behavior:smooth; scroll-padding-top:1rem}
body{margin:0;background:var(--bg);color:var(--ink);
  font:16px/1.65 -apple-system,BlinkMacSystemFont,"Segoe UI",Inter,Roboto,sans-serif}
code,pre,.mermaid{font-family:ui-monospace,SFMono-Regular,Menlo,Consolas,monospace}
.hero{background:linear-gradient(160deg,#0d1b2a,#12304d 55%,#0b6bcb);color:#eaf2fb;padding:3.2rem 1.5rem 2.6rem}
.hero-in{max-width:1180px;margin:0 auto}
.hero h1{font-size:2.5rem;line-height:1.15;margin:0 0 .5rem;letter-spacing:-.02em}
.hero p.sub{font-size:1.1rem;margin:.2rem 0 1rem;color:#bcd4ee;max-width:62ch}
.hero .meta{font-size:.85rem;color:#93b4d8}
.wrap{max-width:1180px;margin:0 auto;display:grid;grid-template-columns:255px 1fr;gap:2.5rem;padding:2rem 1.5rem 4rem}
@media(max-width:900px){.wrap{grid-template-columns:1fr;gap:1rem}#toc{position:static!important;max-height:none!important}}
#toc{position:sticky;top:1rem;align-self:start;max-height:calc(100vh - 2rem);overflow:auto;font-size:.86rem}
#toc h2{font-size:.72rem;letter-spacing:.14em;text-transform:uppercase;color:var(--ink-soft);margin:1.2rem 0 .5rem}
#toc ol{list-style:none;margin:0;padding:0}
#toc a{display:block;padding:.32rem .5rem;border-radius:6px;color:var(--ink);text-decoration:none;border-left:3px solid transparent}
#toc a:hover{background:var(--accent-soft);border-left-color:var(--accent)}
main{min-width:0;max-width:80ch}
.chapter,.card{background:var(--panel);border:1px solid var(--line);border-radius:14px;padding:2rem 2.1rem;margin:0 0 2rem}
@media(max-width:640px){.chapter,.card{padding:1.2rem}}
.chapter>h2{font-size:1.85rem;line-height:1.2;margin:.2rem 0 .3rem;letter-spacing:-.015em}
.chnum{display:inline-block;font-size:.72rem;letter-spacing:.14em;text-transform:uppercase;color:var(--accent);font-weight:700}
.chmeta{font-size:.8rem;color:var(--ink-soft);margin:0 0 1.4rem;padding-bottom:1rem;border-bottom:1px solid var(--line)}
h3{font-size:1.15rem;margin:2rem 0 .6rem;letter-spacing:-.01em}
p{margin:0 0 1rem} b,strong{color:#111} a{color:var(--accent)}
code{background:var(--code-bg);padding:.1em .35em;border-radius:4px;font-size:.88em}
pre{background:#0f1720;color:#e6edf3;padding:1rem 1.1rem;border-radius:10px;overflow:auto;font-size:.82rem;line-height:1.55}
pre code{background:none;padding:0;color:inherit;font-size:1em}
figure.dia{margin:1.8rem 0;padding:1.1rem;background:#fff;border:1px solid var(--line);border-radius:12px}
figure.dia .mermaid{white-space:pre;overflow:auto;font-size:.78rem;color:#33404d;text-align:center}
figure.dia figcaption{margin-top:.9rem;padding-top:.8rem;border-top:1px dashed var(--line);font-size:.85rem;color:var(--ink-soft)}
.recap{background:var(--good-soft);border-radius:10px;padding:1rem 1.2rem;margin:2rem 0 1rem;font-size:.95rem}
.recap h4{margin:0 0 .5rem;font-size:.78rem;letter-spacing:.12em;text-transform:uppercase;color:var(--good)}
.words{border:1px solid var(--line);border-radius:10px;padding:1rem 1.2rem;margin:1rem 0;font-size:.92rem}
.words h4{margin:0 0 .6rem;font-size:.78rem;letter-spacing:.12em;text-transform:uppercase;color:var(--ink-soft)}
.words dl{margin:0;display:grid;grid-template-columns:auto 1fr;gap:.35rem .9rem}
@media(max-width:640px){.words dl{grid-template-columns:1fr}.words dd{margin-bottom:.5rem}}
.words dt{font-weight:700;white-space:nowrap} .words dd{margin:0;color:var(--ink-soft)}
.check{border-left:4px solid var(--accent);background:var(--accent-soft);border-radius:0 10px 10px 0;padding:.9rem 1.2rem;margin:1rem 0;font-size:.93rem}
.check h4{margin:0 0 .4rem;font-size:.78rem;letter-spacing:.12em;text-transform:uppercase;color:var(--accent)}
.refs{margin:1.2rem 0 0;font-size:.9rem}
.refs h4{margin:0 0 .6rem;font-size:.78rem;letter-spacing:.12em;text-transform:uppercase;color:var(--ink-soft)}
.refs ul,ul.refs{list-style:none;margin:0;padding:0}
.refs li.ref{padding:.5rem 0;border-top:1px solid var(--line)}
.tag{display:inline-block;font-size:.66rem;font-weight:700;letter-spacing:.08em;padding:.12rem .45rem;border-radius:4px;color:#fff;margin-right:.45rem;vertical-align:.08em}
.tag.MODEL{background:var(--t1)} .tag.STORAGE{background:var(--t2)} .tag.DIST{background:var(--t3)}
.tag.TXN{background:var(--t4)} .tag.STREAM{background:var(--t5)} .tag.OPS{background:var(--t6)}
.note{font-size:.85rem;color:var(--ink-soft);font-style:italic}
table{border-collapse:collapse;width:100%;margin:1.4rem 0;font-size:.88rem}
th,td{border:1px solid var(--line);padding:.5rem .65rem;text-align:left;vertical-align:top}
th{background:var(--code-bg);font-size:.8rem}
#filters button,#answerctl{font:inherit;cursor:pointer;border:1px solid var(--line);background:#fff;border-radius:999px;padding:.25rem .7rem;margin:.15rem .25rem .15rem 0}
#filters button.off{opacity:.35}
footer{border-top:1px solid var(--line);padding:2rem 1.5rem;text-align:center;color:var(--ink-soft);font-size:.85rem}
details.ans{margin:.35rem 0 1rem;background:var(--panel);border:1px solid var(--line);border-radius:8px;padding:.5rem .85rem}
details.ans>summary{cursor:pointer;font-size:.72rem;letter-spacing:.11em;text-transform:uppercase;color:var(--accent);font-weight:700;list-style:none}
details.ans[open]>summary{padding-bottom:.4rem;margin-bottom:.2rem;border-bottom:1px solid var(--line)}
details.ans p{margin:.55rem 0;font-size:.9rem}
details.ans p:last-child{margin-bottom:.15rem}

/* ---- book-guided components ---- */
details>summary::-webkit-details-marker{display:none}
details>summary::before{content:"\25B8  "}
details[open]>summary::before{content:"\25BE  "}
.hero-ch{padding:2.2rem 1.5rem 1.8rem}
.hero .crumbs{font-size:.8rem;margin:0 0 .6rem;color:#93b4d8}
.hero .crumbs a{color:#cfe2f7}
.spine{font-style:italic;color:var(--ink)}
.readnow{background:var(--accent-soft);border-color:#bcd6f2;text-align:center}
.readnow h2{margin:.2rem 0 .4rem;font-size:1.3rem}
.how{display:inline-block;font-size:.68rem;font-weight:700;letter-spacing:.08em;text-transform:uppercase;padding:.12rem .45rem;border-radius:4px;white-space:nowrap}
.how.close{background:var(--warm-soft);color:var(--warm)}
.how.skim{background:var(--code-bg);color:var(--ink-soft)}
.how.later{border:1px dashed var(--line);color:var(--ink-soft)}
.carry,.carried,.bookq,.system,.lab,.quiz{margin:1.6rem 0;font-size:.93rem}
.carry h4,.carried h4,.bookq h4,.system h4,.lab h4,.quiz h4,.revision h4{margin:0 0 .5rem;font-size:.78rem;letter-spacing:.12em;text-transform:uppercase}
.carry{border:1px dashed var(--accent);border-radius:10px;padding:.9rem 1.2rem}
.carry h4,.carried h4{color:var(--accent)}
.carried{border-left:4px solid var(--accent);background:var(--accent-soft);border-radius:0 10px 10px 0;padding:.9rem 1.2rem}
.bookq{border-left:4px solid var(--book);background:var(--book-soft);border-radius:0 10px 10px 0;padding:.9rem 1.2rem}
.bookq h4{color:var(--book)}
.pageref{font-size:.74rem;color:var(--ink-soft);white-space:nowrap}
.system{border:1px solid var(--line);border-top:4px solid var(--sys);border-radius:10px;padding:1rem 1.2rem;background:#fff}
.system h4{color:var(--sys)}
.system .here{background:var(--sys-soft);border-radius:8px;padding:.6rem .8rem}
.system.back{border-top-width:2px}
.system .drift{font-size:.8rem;color:var(--ink-soft);border-top:1px dashed var(--line);padding-top:.5rem;margin:.8rem 0 0}
.lab{background:var(--warm-soft);border:1px solid #f0d9ae;border-left:4px solid var(--warm);border-radius:10px;padding:1rem 1.2rem}
.lab h4{color:var(--warm)}
.labmeta{font-size:.8rem;color:var(--ink-soft);margin:-.2rem 0 .8rem}
details.hint,details.sol{margin:.4rem 0;background:#fff;border:1px solid #f0d9ae;border-radius:8px;padding:.45rem .85rem}
details.sol{border-color:var(--good)}
details.hint>summary,details.sol>summary{cursor:pointer;font-size:.72rem;letter-spacing:.11em;text-transform:uppercase;font-weight:700;list-style:none}
details.hint>summary{color:var(--warm)} details.sol>summary{color:var(--good)}
.quiz{background:#10233a;color:#dbe7f3;border-radius:12px;padding:1.1rem 1.3rem}
.quiz h4,.quiz a{color:#8fc1f5}
.quiz code{background:#1b3553;color:#e6edf3}
.quiz .note{color:#a9bfd6}
.quiz ul.strands{columns:2;margin:.3rem 0 .8rem;padding-left:1.1rem}
@media(max-width:640px){.quiz ul.strands{columns:1}}
.quiz pre.prompt{background:#0a1826;border:1px solid #24466b;white-space:pre-wrap}
.quiz pre code{background:none;padding:0;color:inherit}
.quiz button.copy{font:inherit;font-size:.8rem;cursor:pointer;border:1px solid #24466b;background:#16304d;color:#dbe7f3;border-radius:999px;padding:.2rem .8rem;margin-bottom:.6rem}
.revision{border-left:4px solid var(--book)}
.revision h4{color:var(--book)}
.status{font-size:.7rem;font-weight:700;letter-spacing:.06em;text-transform:uppercase;padding:.1rem .45rem;border-radius:4px;background:var(--code-bg);color:var(--ink-soft);white-space:nowrap}
.status.probed{background:var(--accent-soft);color:var(--accent)}
.status.edges{background:var(--warm-soft);color:var(--warm)}
.status.locked{background:var(--good-soft);color:var(--good)}
.chnav{max-width:1180px;margin:0 auto;padding:0 1.5rem 2rem;display:flex;justify-content:space-between;gap:1rem;font-size:.9rem}

/* ---- zoomable figures: a button in each figure's corner opens its diagram full screen (§12.6) ---- */
figure.dia{position:relative;padding-top:2.2rem}
figure.dia .zoombtn{position:absolute;top:.45rem;right:.55rem;font:inherit;font-size:.7rem;letter-spacing:.04em;cursor:zoom-in;border:1px solid var(--line);background:#fff;color:var(--ink-soft);border-radius:999px;padding:.1rem .55rem;opacity:.8}
figure.dia .zoombtn:hover{opacity:1;border-color:var(--accent);color:var(--accent)}
#zoomlayer{position:fixed;inset:0;z-index:50;background:rgba(13,27,42,.88);display:none}
#zoomlayer.on{display:block}
#zoomlayer .zl-stage{position:absolute;inset:3.1rem .9rem .9rem;overflow:hidden;background:#fff;border-radius:12px;cursor:grab;touch-action:none}
#zoomlayer .zl-stage.drag{cursor:grabbing}
#zoomlayer .zl-inner{transform-origin:0 0;position:absolute;left:0;top:0}
#zoomlayer .zl-inner svg{display:block;max-width:none!important}
#zoomlayer .zl-bar{position:absolute;top:.6rem;left:.9rem;right:.9rem;display:flex;gap:.4rem;align-items:center;color:#cfe0f3;font-size:.82rem}
#zoomlayer .zl-bar button{font:inherit;cursor:pointer;border:1px solid #3d5a78;background:#12304d;color:#eaf2fb;border-radius:999px;padding:.18rem .75rem}
#zoomlayer .zl-bar .zl-cap{flex:1;overflow:hidden;white-space:nowrap;text-overflow:ellipsis;margin-left:.6rem}

@media print{.hero{background:#fff;color:#000}.hero p.sub,.hero .meta,.hero .crumbs{color:#333}#toc,.chnav,button,#zoomlayer{display:none}
  .wrap{display:block}.chapter{border:none;padding:0}#before{page-break-after:always}
  .quiz{background:#fff;color:#000;border:1px solid var(--line)}.quiz pre.prompt{background:#f4f6f8;color:#000}}
```

### 12.4 Script — `assets/dossier.js`

```js
// reveal-all: answers only; hints and solutions stay closed
const ctl = document.getElementById('answerctl');
if (ctl) ctl.addEventListener('click', () => {
  const all = [...document.querySelectorAll('details.ans')];
  const open = all.every(d => d.open);
  all.forEach(d => d.open = !open);
  ctl.textContent = open ? 'Open all answers' : 'Close all answers';
});
// a collapsed <details> does not print: open everything first
window.addEventListener('beforeprint', () => document.querySelectorAll('details').forEach(d => d.open = true));
// copy buttons on quiz prompts
document.querySelectorAll('button.copy').forEach(b => b.addEventListener('click', async () => {
  const pre = b.previousElementSibling;
  try { await navigator.clipboard.writeText(pre.innerText); b.textContent = 'Copied'; }
  catch (e) {  // no clipboard permission: select the text instead
    const r = document.createRange(); r.selectNodeContents(pre);
    const s = getSelection(); s.removeAllRanges(); s.addRange(r);
    b.textContent = 'Selected, press Ctrl+C';
  }
  setTimeout(() => b.textContent = 'Copy prompt', 2000);
}));
// layer filter on the hub's reference list
document.querySelectorAll('#filters button').forEach(b => b.addEventListener('click', () => {
  b.classList.toggle('off');
  const off = [...document.querySelectorAll('#filters button.off')].map(x => x.dataset.l);
  document.querySelectorAll('li.ref').forEach(li => li.style.display = off.includes(li.dataset.layer) ? 'none' : '');
}));
/* Zoom: every figure gets a button that opens its diagram full screen.
   Wheel or pinch zooms around the pointer, drag pans, double-click fits, Esc closes.
   Same code as the parent spec's zoom section; it reads the SVG at click time, so it
   does not wait for Mermaid. */
(function(){
  const layer=document.createElement('div'); layer.id='zoomlayer';
  layer.innerHTML='<div class="zl-bar"><button type="button" data-z="in">+</button><button type="button" data-z="out">&minus;</button><button type="button" data-z="fit">Fit</button><button type="button" data-z="close">Close (Esc)</button><span class="zl-cap"></span></div><div class="zl-stage"><div class="zl-inner"></div></div>';
  document.body.appendChild(layer);
  const stage=layer.querySelector('.zl-stage'), inner=layer.querySelector('.zl-inner'), cap=layer.querySelector('.zl-cap');
  let s=1,x=0,y=0,w=0,h=0,drag=null;
  const apply=()=>{inner.style.transform='translate('+x+'px,'+y+'px) scale('+s+')';};
  function fit(){
    if(!w||!h) return;
    const W=stage.clientWidth, H=stage.clientHeight;
    s=Math.min(W/w,H/h)*0.94; x=(W-w*s)/2; y=(H-h*s)/2; apply();
  }
  function zoomAt(f,cx,cy){
    const r=stage.getBoundingClientRect(), px=cx-r.left, py=cy-r.top;
    x=px-(px-x)*f; y=py-(py-y)*f; s*=f; apply();
  }
  function zoomMid(f){const r=stage.getBoundingClientRect(); zoomAt(f,r.left+r.width/2,r.top+r.height/2);}
  function open(fig){
    const svg=fig.querySelector('svg'); if(!svg) return;
    const c=svg.cloneNode(true);
    const vb=(svg.getAttribute('viewBox')||'').trim().split(/[\s,]+/).map(Number);
    const r=svg.getBoundingClientRect();
    w=(vb.length===4&&vb[2])?vb[2]:r.width; h=(vb.length===4&&vb[3])?vb[3]:r.height;
    c.setAttribute('width',w); c.setAttribute('height',h);
    c.style.maxWidth='none'; c.style.width=w+'px'; c.style.height=h+'px';
    inner.innerHTML=''; inner.appendChild(c);
    const fc=fig.querySelector('figcaption'); cap.textContent=fc?fc.textContent.replace(/\s+/g,' ').split(/\.\s/)[0]:'';
    layer.classList.add('on'); document.body.style.overflow='hidden';
    requestAnimationFrame(fit);
  }
  function close(){layer.classList.remove('on'); document.body.style.overflow=''; inner.innerHTML='';}
  layer.addEventListener('click',e=>{
    const b=e.target.closest('button'); if(!b) return;
    ({in:()=>zoomMid(1.25),out:()=>zoomMid(0.8),fit:fit,close:close})[b.dataset.z]();
  });
  stage.addEventListener('wheel',e=>{e.preventDefault(); zoomAt(e.deltaY<0?1.12:1/1.12,e.clientX,e.clientY);},{passive:false});
  stage.addEventListener('pointerdown',e=>{drag={px:e.clientX,py:e.clientY,x:x,y:y}; stage.classList.add('drag'); stage.setPointerCapture(e.pointerId);});
  stage.addEventListener('pointermove',e=>{if(!drag) return; x=drag.x+e.clientX-drag.px; y=drag.y+e.clientY-drag.py; apply();});
  stage.addEventListener('pointerup',()=>{drag=null; stage.classList.remove('drag');});
  stage.addEventListener('dblclick',fit);
  window.addEventListener('resize',()=>{if(layer.classList.contains('on')) fit();});
  document.addEventListener('keydown',e=>{
    if(!layer.classList.contains('on')) return;
    if(e.key==='Escape') close(); else if(e.key==='+'||e.key==='=') zoomMid(1.25); else if(e.key==='-') zoomMid(0.8); else if(e.key==='0') fit();
  });
  document.querySelectorAll('figure.dia').forEach(fig=>{
    const b=document.createElement('button'); b.type='button'; b.className='zoombtn';
    b.textContent='⤢ zoom'; b.title='Open full screen: scroll to zoom, drag to pan, Esc to close';
    b.addEventListener('click',()=>open(fig)); fig.appendChild(b);
  });
})();
```

### 12.5 Build order

- **Once, at the start:** `assets/`, the hub with every chapter listed as `not built`, `NOTES.md`, and `audit.py`.
- **Each chapter:** write the skeleton with `<!--APPEND-->` inside the after half, then fill it in three or four edits (the before half; the carried answers, one-screen summary and key learnings; systems and labs; questions, recap, references and quiz). Remove the marker at the end. Do not emit a whole chapter in one tool call; a truncated write costs more to repair than three clean ones.
- **After each chapter:** update the hub (status, glossary, systems index, references, revision log), `NOTES.md` (terms JSON, systems, status), and the previous page's "next" link. Then run the audit.
- **Escaping:** code blocks that contain HTML or XML must be entity-escaped (`&lt;`, `&gt;`, `&amp;`). Shell, YAML, SQL and JSON usually need nothing. Check every `<` inside a `<pre>`.

### 12.6 Diagrams

- **One diagram in the before half** (the mental model) and **at least one in the after half.** Plan about three per chapter.
- Mermaid, in `<div class="mermaid">`. The div's text is the source, so it stays readable if the CDN fails.
- **Every diagram sits in `<figure class="dia">` and can be zoomed.** At the width of the text column, a wide sequence diagram has labels a few pixels high. `assets/dossier.js` (§12.4) gives every `figure.dia` a `⤢ zoom` button that opens the diagram full screen. In that view the wheel or a pinch zooms around the pointer, dragging pans, a double-click fits, and Esc closes. The bar at the top shows the caption up to its first full stop, so the `Diagram N.M — <what it is>.` opening is also the zoom title. A bare `div.mermaid` gets no button. A hand-drawn `<svg>` in a figure zooms too, if it has a `viewBox`.
- **Keep `deterministicIds:true`** in `mermaid.initialize` (§12.1). Without it Mermaid names each SVG after the clock. Two diagrams rendered in the same millisecond collide and one comes out blank, and the zoom then copies the broken one.
- **A diagram never contains a term the page has not defined yet.** In the before half, only words from the word list and earlier chapters.
- Use the same bolded vocabulary as the text: identical words, never synonyms.
- Every caption starts with **`Diagram N.M — <what it is>.`** followed by "Notice …", which tells the reader where to look.
- Colour carries meaning: highlight the one box that matters (`style X fill:#e8f1fb,stroke:#0b6bcb,stroke-width:2px`), red for the dangerous or slow path, green for the safe or fast one.
- Distinguish setup from runtime with line weight: dotted `-.->` for one-time control flow, thick `==>` for per-request data flow.
- When redrawing a figure from the book, cite it in the caption ("after the book's figure on p. 303") and simplify it to what this page needs.

| Need | Use |
|---|---|
| Compare 2–4 approaches side by side | `flowchart LR`, one `subgraph` per approach |
| Layered structure, who contains whom | `flowchart TB` with nested subgraphs |
| Ordered exchange between parties | `sequenceDiagram` |
| A race or a failure with a decision | `sequenceDiagram` with `alt / else / end` |
| States a thing moves through | `stateDiagram-v2` |

Mermaid traps:

- **Quote every label:** `A["text here"]`. Unquoted parentheses, colons and slashes break the parse.
- **No `&`, `<` or `>` inside a label.** The browser eats them as HTML. Write "and", "under", "over". (Arrow syntax like `-->` is fine.)
- **No parentheses in `sequenceDiagram` participant aliases:** `participant D as Driver, host or guest`.
- `<br/>` works inside labels and is the right way to wrap.
- Edge labels: `A -->|"text"| B`. Keep them short.
- The first line inside the div must be the diagram type. A stray blank line or comment first kills it silently.

---

## 13. Phase 5 — Self-audit (mandatory, scripted)

Do not eyeball this. Run it from the `dossier/` folder after every chapter. It reads the terms map and the budget from `NOTES.md`, so it needs no editing per book.

```python
# audit.py: run from the dossier folder (the one holding index.html and NOTES.md)
import re, os, glob, json, html
from html.parser import HTMLParser

NOTES = open('NOTES.md', encoding='utf-8').read() if os.path.exists('NOTES.md') else ''
def fenced(tag, default):
    m = re.search(r'```json %s\n(.*?)\n```' % tag, NOTES, re.S)
    return json.loads(m.group(1)) if m else default
TERMS  = fenced('terms', {})                          # {"replication log": "ch06", ...}
BUDGET = fenced('budget', {"before": 10, "after": 20}) # minutes, from the reader contract
WPM = 200

PAGES = sorted(glob.glob('ch[0-9][0-9].html'))
ALL = PAGES + [f for f in ('index.html', 'capstone.html') if os.path.exists(f)]
bad = 0
def flag(*a):
    global bad; bad += 1; print('   !!', *a)

def read(f): return open(f, encoding='utf-8').read()
def strip(fr):
    fr = re.sub(r'<div class="mermaid">.*?</div>', ' ', fr, flags=re.S)
    fr = re.sub(r'<details class="(hint|sol)">.*?</details>', ' ', fr, flags=re.S)
    return ' '.join(html.unescape(re.sub(r'<[^>]+>', ' ', fr)).split())
def words(fr):           # reading time: prose only; code and prompts are run or copied, not read
    return len(strip(re.sub(r'<pre.*?</pre>', ' ', fr, flags=re.S)).split())
def blocks(s, cls):      # cards never nest a <div>, so a lazy match is safe
    return re.findall(r'<div class="%s(?: [^"]*)?"[^>]*>(.*?)</div>' % cls, s, re.S)
def section(s, sid):
    m = re.search(r'<section id="%s"[^>]*>(.*?)</section>' % sid, s, re.S)
    return m.group(1) if m else ''
def main_of(s):
    body = s[s.index('<main>'):s.index('</main>')] if '<main>' in s else s
    return re.sub(r'<section id="revision[\w-]*".*?</section>', ' ', body, flags=re.S)

VOID = {'meta', 'br', 'hr', 'img', 'link', 'input', 'source', 'wbr'}
class Nest(HTMLParser):
    def __init__(s): super().__init__(); s.st = []; s.err = []; s.ids = set()
    def _id(s, a):
        i = dict(a).get('id')
        if i in s.ids: s.err.append(('duplicate id', i))
        if i: s.ids.add(i)
    def handle_startendtag(s, t, a): s._id(a)
    def handle_starttag(s, t, a):
        s._id(a)
        if t not in VOID: s.st.append((t, s.getpos()))
    def handle_endtag(s, t):
        if t in VOID: return
        if not s.st: s.err.append(('extra close', t, s.getpos())); return
        if s.st[-1][0] != t: s.err.append(('mismatch', t, 'open:' + s.st[-1][0], s.getpos()))
        else: s.st.pop()

# --- 1. every file: nesting, markers, mermaid ---------------------------------
IDS = {}
print('== files')
if 'zoomlayer' not in (read('assets/dossier.js') if os.path.exists('assets/dossier.js') else ''):
    flag('assets/dossier.js has no zoom script (§12.4)')
for f in ALL:
    s = read(f); p = Nest(); p.feed(s); IDS[f] = p.ids
    print(f)
    if p.st: flag('unclosed', p.st[:3])
    for e in p.err[:5]: flag(*e)
    if '<!--APPEND-->' in s: flag('leftover APPEND marker')
    if '<div class="mermaid">' in s:
        if 'deterministicIds:true' not in s: flag('mermaid.initialize without deterministicIds:true')
        if 'assets/dossier.js' not in s: flag('diagrams but no assets/dossier.js: no zoom buttons')
        loose = s.count('<div class="mermaid">') - len(re.findall(r'<figure class="dia">\s*<div class="mermaid">', s))
        if loose: flag(loose, 'diagram(s) outside <figure class="dia">: no zoom button')
    for i, m in enumerate(re.findall(r'<div class="mermaid">(.*?)</div>', s, re.S), 1):
        head = m.strip().split('\n')[0]
        if not head.startswith(('flowchart', 'sequenceDiagram', 'graph', 'stateDiagram', 'timeline')):
            flag('mermaid', i, 'bad first line:', head[:40])
        if '&' in m or re.search(r'<(?!br\s*/?>)', m): flag('mermaid', i, 'has & or < in a label')
        if re.search(r'participant [^\n]* as [^\n]*\(', m): flag('mermaid', i, 'parens in participant alias')

# --- 2. links and anchors across files ----------------------------------------
print('== links')
for f in ALL:
    for href in re.findall(r'href="([^"]+)"', read(f)):
        if re.match(r'(https?|mailto):', href): continue
        path, _, anchor = href.partition('#')
        path = path or f
        if not os.path.exists(path): flag(f, 'broken link', href); continue
        if anchor and path.endswith('.html') and anchor not in IDS.get(path, set()):
            flag(f, 'missing anchor', href)

# --- 3. chapter pages ----------------------------------------------------------
print('== chapter pages')
FIRST, WORDS = {}, {}
for f in PAGES:
    s = read(f); print(f)
    before, after = section(s, 'before'), section(s, 'after')
    if not before or not after: flag('missing the before or after section'); continue
    if not section(s, 'book'): flag('missing the read-the-book card')
    for half, fr in (('before', before), ('after', after)):
        w = words(fr); got = max(1, round(w / WPM))
        st = re.search(r'~(\d+) min %s' % half, s)
        print('   %-6s %5d words  ~%d min' % (half, w, got))
        if st and int(st.group(1)) != got: flag(half, 'stated ~%s min, measured ~%d' % (st.group(1), got))
        if got > BUDGET[half] * 1.25: flag(half, 'over budget: ~%d min against %d' % (got, BUDGET[half]))
    if 'class="spine"' not in before: flag('no spine sentence in the metadata line')
    fb, fa = before.count('<figure class="dia">'), after.count('<figure class="dia">')
    if fb < 1 or fa < 1: flag('diagrams before %d, after %d; need one in each half' % (fb, fa))
    if s.count('<figure class="dia">') != s.count('<figcaption>'): flag('figure and caption counts differ')
    # word list
    wl = blocks(before, 'words')
    WORDS[f[:4]] = [strip(x).lower() for x in re.findall(r'<dt[^>]*>(.*?)</dt>', wl[0], re.S)] if wl else []
    if not WORDS[f[:4]]: flag('no "Words you will meet" list')
    # carried questions: asked before, answered after
    carry, carried = blocks(before, 'carry'), blocks(after, 'carried')
    nq = carry[0].count('<li') if carry else 0
    na = carried[0].count('<details class="ans">') if carried else 0
    if nq < 3 or nq != na: flag('carry-in questions %d, answered after reading %d' % (nq, na))
    # every question has an answer
    total = 0
    for cls in ('carried', 'bookq', 'check'):
        for blk in blocks(after, cls):
            q = len(re.findall(r'<p>\d+\.\s', blk)); d = blk.count('<details class="ans">')
            if cls != 'carried': total += q
            thin = [n for n in (len(strip(a).split()) for a in
                    re.findall(r'<details class="ans">(.*?)</details>', blk, re.S)) if n < 35]
            if q != d: flag(cls, '%d questions, %d answers' % (q, d))
            if thin: flag(cls, 'thin answers (word counts):', thin)
    if total < 5: flag('only %d book and check-yourself questions; want at least 5' % total)
    # labs
    labs = blocks(after, 'lab')
    if not 2 <= len(labs) <= 3: flag('%d labs; want 2 or 3' % len(labs))
    for i, lab in enumerate(labs, 1):
        h, so = lab.count('<details class="hint">'), lab.count('<details class="sol">')
        if h < 2 or so != 1: flag('lab %d: %d hints, %d solutions' % (i, h, so))
        for need in ('class="labmeta"', 'Done when'):
            if need not in lab: flag('lab %d: missing %s' % (i, need))
    # real systems: one full card per system per book, back cards afterwards
    for m in re.finditer(r'<div class="system( back)?"([^>]*)>(.*?)</div>', after, re.S):
        back, attrs, body = m.groups()
        sm = re.search(r'data-system="([\w-]+)"', attrs)
        if not sm: flag('system card without data-system'); continue
        slug = sm.group(1)
        if back:
            if slug not in FIRST: flag('back card for %s but no earlier full card' % slug)
            elif '%s#sys-%s' % (FIRST[slug], slug) not in body: flag('back card %s does not link to its full card' % slug)
        else:
            if slug in FIRST: flag('second full card for %s (first in %s); use a back card' % (slug, FIRST[slug]))
            FIRST.setdefault(slug, f)
            for need in ('class="here"', '<pre', 'What you should see', 'class="drift"'):
                if need not in body: flag('system %s: missing %s' % (slug, need))
    # key learnings = quiz strands, each with a page reference
    strands = re.findall(r'<h3 class="strand" id="s-([\w-]+)"', after)
    if not 4 <= len(strands) <= 7: flag('%d key learnings; want 4 to 7' % len(strands))
    for part in re.split(r'<h3 class="strand"', after)[1:]:
        sid = re.match(r' id="s-([\w-]+)"', part)
        part = re.split(r'<h3|<div class="(?:system|lab)', part)[0]
        if not re.search(r'pp?\.\s?\d+', part): flag('learning', sid.group(1) if sid else '?', 'has no page reference')
    quizzes = re.findall(r'<div class="quiz" data-when="(before|after)"[^>]*>(.*?)</div>', s, re.S)
    if sorted(w for w, _ in quizzes) != ['after', 'before']: flag('quiz cards found:', [w for w, _ in quizzes])
    for w, q in quizzes:
        listed = re.findall(r'data-strand="([\w-]+)"', q)
        if set(listed) != set(strands): flag('quiz', w, 'strands differ from key learnings:', sorted(set(listed) ^ set(strands)))
        if 'class="prompt"' not in q: flag('quiz', w, 'no prompt block')

# --- 4. vocabulary: no term before the chapter that defines it -----------------
print('== vocabulary')
TEXT = {f[:4]: strip(main_of(read(f))).lower() for f in PAGES}
for term, home in TERMS.items():
    pat = re.compile(r'\b%s\b' % re.escape(term.lower()))
    for ch, t in TEXT.items():
        if int(ch[2:]) >= int(home[2:]): continue
        m = pat.search(t)
        if m: flag('EARLY USE %r in %s (defined in %s): ...%s...' % (term, ch, home, t[max(0, m.start()-60):m.end()+30]))
    if home in WORDS and not any(term.lower() in w for w in WORDS[home]):
        flag('%r is mapped to %s but is not in its word list' % (term, home))

# --- 5. hub consistency --------------------------------------------------------
print('== hub')
if os.path.exists('index.html'):
    hub = read('index.html'); gloss = strip(section(hub, 'glossary')).lower()
    for f in PAGES:
        if not re.search(r'href="%s(#[^"]*)?"' % re.escape(f), hub): flag('hub does not link', f)
        for w in WORDS.get(f[:4], []):
            if w not in gloss: flag('glossary is missing %r from %s' % (w, f))
    for slug, f in FIRST.items():
        if 'systems-%s' % slug not in IDS['index.html']: flag('systems index has no row for', slug)
else:
    flag('no index.html')

print('\n%d problem(s)' % bad)
```

Then fix, by hand:

- Every **EARLY USE**. Rewrite it in plain words with the later chapter's name, or move the definition earlier if the book allows it. An early use of an everyday word is a bad terms entry: change the entry to the multi-word term of art ("replication log" rather than "log").
- Diagram labels containing an undefined term. The script cannot see these; re-read every label against the word lists once.
- Stated reading times that disagree with the measured ones. Patch them by line number, because repeated strings like `~20 min after` replace each other's output under a string replace.
- The things the audit cannot judge: the question framings (§9.2), page references in answers, "Done when" lines that are actually observable, and system cards whose "where this chapter lives" paragraph actually maps strands to parts.

Then render the page, if a browser is available: `google-chrome-stable --headless=new --virtual-time-budget=30000 --dump-dom "file://$PWD/chNN.html"`. Every `div.mermaid` should hold an `<svg>` with a `viewBox`, no two SVGs should share an `id`, and no `div.mermaid` should contain "Syntax error". Open one diagram in the zoom layer, by hand or with a test script that clicks a `.zoombtn`. If you inject a test script, search for "Syntax error" inside the diagram divs only, because the script contains the string and matches itself.

Report the result honestly in the message that delivers the chapter, including anything unverified: "demos not executed, no Docker here", "diagrams not rendered, no browser".

---

## 14. Phase 6 — After delivery, probe again

A page is a draft until the reader has used it.

### 14.1 After the first chapter

Ask no more than three, through the question tool where the options are enumerable:

1. "Which sentence in the before half did you have to read twice?" That is a definition failure; fix it and everything downstream of it.
2. "Did the primer spoil the chapter, leave you lost in it, or sit about right?" Recalibrate §5.1 for every later chapter.
3. "Was any exercise too big, too small, or blocked by setup?" Recalibrate §8.1 and the reader contract.

Record the answers in the reader contract. They change every page that follows, which is why they are asked after the first chapter and not the fifth.

### 14.2 Every chapter

Offer the lock-in quiz explicitly: *"Want the lock-in quiz on this chapter now? Whatever you stumble on gets fixed in the page."* Then run §10 and write the revision card.

### 14.3 Standing offers

Name these so the reader knows the format can grow:

- **Spaced review.** A hub section listing every strand still `edge` across chapters, with a prompt that re-quizzes them after a week.
- **Troubleshooting appendix.** Symptom → which chapter's mechanism is failing → the command that confirms it. High value once the reader starts using the ideas at work.
- **One-page cheat sheet per chapter.** The mental-model diagram, the word list and the recap, printable on one sheet.
- **A deeper lab** for a system the reader wants to go further with, cross-linked to the chapters that explain each step.
- **A "what changed" refresh** for drifting system cards, dated, when the tooling moves.
- **Trim to a target.** If a chapter overshot, offer the specific cuts from §5.4 instead of trimming silently.

---

## 15. Failure modes

| Symptom | Cause | Fix |
|---|---|---|
| The summary says something the book does not | Written from memory, or from another edition | Re-read the pages; every claim gets a printed page reference (§1.1) |
| The book feels redundant after the primer | The before half taught the mechanisms | Cut it back to vocabulary, shape and questions (§5.1) |
| The reader is lost in the book despite the primer | A term the book uses early was missing from the word list | Add it; skim the chapter's first pages for jargon while building the inventory |
| A later chapter's term appears early | A book forward reference or a system card pulled it in | Plain words and the chapter's name (§1.2); run the audit |
| A system card reads like a product page | The "where this chapter lives" paragraph is thin | Rewrite it as a mapping from strands to parts (§7.2) |
| A demo fails on the reader's machine | Unverified image tag or command, or the wrong environment | Run it, date it, write it against the reader contract (§4) |
| Exercises get skipped | Too big, needs setup, or depends on an earlier lab | 20–60 minutes, standalone, one paper-only per chapter (§8.1) |
| The reader is confident and wrong | Recall-only questions | Add diagnosis and predict-the-value questions (§9.2) |
| Chapter pages feel like separate notes | No point-backs; no capstone | Mechanical point-backs (§6.2); build the capstone (§11) |
| Quiz results feel good, retention does not | Pasted A/B/C/D, or scoring letters | Use the harness question tool; score the reason (§10.3) |
| Quiz findings live only in `.alvar/` | No revision card | Write one (§10.5) |
| A chapter runs over budget | Breadth crept in | Cut in the §5.4 order |
| Links rot or are wrong | Guessed URLs | Verify; name the resource when a URL cannot be verified (§1.5) |
| A truncated page mid-diagram | Too much written per tool call | Three or four edits per chapter, `<!--APPEND-->` marker (§12.5) |
| A wide diagram is unreadable | Drawn at column width with no way to enlarge it | Zoom script in `assets/dossier.js`; every diagram inside `figure.dia` (§12.6) |
| A diagram renders blank, or two draw into one | Mermaid ids come from the clock and collide | `deterministicIds:true` (§12.1) |
| A resumed session contradicts earlier chapters | Decisions lived only in the chat | Keep `NOTES.md` current after every chapter (§0.1) |

---

## 16. Quick checklist

**Once per book:**

- [ ] Book facts gathered before asking anything: edition, chapters, printed-page offset, exercises yes or no, preface read
- [ ] Reader contract recorded in `NOTES.md`; `LEARNER.md` offered
- [ ] Book plan approved: per-chapter spines, terms, systems, exercises, connecting spine, layer tags
- [ ] Hub, assets (including the zoom styles and script), `NOTES.md` and `audit.py` in place; the how-to-use card says diagrams zoom

**Every chapter:**

- [ ] Chapter read from the file; inventory in `NOTES.md`; 4–7 strands named
- [ ] Before half: metadata line with spine sentence, the question, word list, mental-model diagram, reading guide, three carried questions, pre-read quiz card
- [ ] Read-the-book card with pages and time
- [ ] After half: carried answers with pages, the chapter on one screen, one key learning per strand with a page reference and a worked example, system cards (full or back), 2–3 labs with hint ladders and solutions, questions in the required mix, recap, verified references, lock-in quiz card
- [ ] Every question has a `<details class="ans">` answer that names the mechanism and the page
- [ ] Demos and solutions run, or plainly labelled as not executed
- [ ] Every diagram inside `figure.dia`; diagrams rendered in a browser and one opened in the zoom layer, or plainly labelled as not rendered
- [ ] Prose checked against both ban lists and the banned sentence shapes; nothing referred to by a bare number
- [ ] Hub updated: status, glossary, systems index, references, revision log; previous page's next link
- [ ] Audit run and clean, or every remaining flag explained in the delivery message
- [ ] After a quiz round: revision card, hub status and revision log updated from the map

**At the end:**

- [ ] Capstone built: one stop per chapter, tower and sequence diagrams, payoff sentence, cross-book quiz card
- [ ] Follow-up probe questions asked; standing offers named

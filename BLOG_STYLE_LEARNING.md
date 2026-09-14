# AGENT.md — Visual Dossier Builder

A reusable spec for producing **chaptered, diagram-rich, vocabulary-disciplined learning documents** on any technical topic, as a single self-contained HTML file.

Reference implementation: `sr-iov/sr-iov-dossier.html` (SR-IOV, hardware → kernel → KVM → Kubernetes → KubeVirt). Read it before starting if it exists; it is the shape you are aiming for.

---

## 0. What you are producing

One HTML file. Inside it:

- **8–12 chapters**, each 900–1,600 words, in dependency order (never alphabetical, never "by importance")
- **One continuous narrative spine** — a single object or event whose journey every chapter advances
- **≥1 diagram per chapter**, Mermaid, each with a 1–3 sentence caption
- **Cumulative glossary** — per-chapter "New words", fully merged at the end
- **Check-yourself questions with answers behind a toggle** — every question, every chapter (§4.5)
- **Verified references** per chapter, layer-tagged
- Sticky table of contents, per-chapter reading times, layer-tag filter, print CSS

You are not writing documentation. You are not writing a tutorial. You are writing the thing a smart colleague would write on a whiteboard over two hours, if the whiteboard were infinite and they had time to draw properly.

---

## 1. The four non-negotiables

Everything else is adjustable. These are not.

### 1.1 Progressive vocabulary

**Never use a term of art before defining it.** No forward references, ever.

- First use: **bold** the term, define it in the same sentence or the next, in plain language. Add an analogy if it helps.
- Expand every acronym on first use. Always. `SR-IOV → Single Root I/O Virtualization`.
- Before writing a chapter, list the terms it needs. Verify each is already defined or gets defined in that chapter. If a chapter needs a term from a later chapter, **restructure the chapter order** — do not hand-wave, do not say "we'll cover this later".
- Two allowed exceptions: (a) everyday engineering words the reader profile already owns; (b) a term appearing inside the *title of a cited reference* — that's a citation, not usage.
- If you truly must sign-post forward, name the *thing* in plain words and the chapter, never the term: "the safety half needs one more piece of hardware, and that is chapter 2" — not "we need the IOMMU, see chapter 2".

### 1.2 The narrative spine

Pick one object and follow it from birth to work. Every chapter opens by locating the reader on that journey.

Examples of good spines:
| Topic | Spine |
|---|---|
| SR-IOV | "The life of a Virtual Function" — capability in silicon → PCI device → VFIO handoff → k8s resource → interface in a VM → packet on the wire |
| TLS | "The life of one handshake" |
| Databases | "The life of a row" — client bytes → parser → plan → buffer pool → WAL → disk → replica |
| Kubernetes scheduling | "The life of a pod that hasn't been placed yet" |
| Compilers | "The life of one `if` statement" |
| Git | "The life of one line of code" — working tree → index → object store → packfile → remote |

The spine sentence goes in the chapter's metadata line: *"Last chapter our VF became a real PCI device; now the kernel has to keep it honest."* Never skip it. It is what turns 11 essays into one document.

### 1.3 Grounding

Every abstract claim gets grounded within a sentence or two: a command, a file path, a register name, a line of YAML, a number, or a diagram callout. If a paragraph has no concrete anchor, it is decoration — cut it or ground it.

### 1.4 Verified references only

Verify every URL with web tools before it goes in the file. If you cannot verify it, **name the resource and how to find it** instead of guessing a URL ("PCI-SIG SR-IOV 1.1 specification, members-only from the PCI-SIG specifications library"). Never invent a title, author, date, or link.

---

## 2. Phase 0 — Probe the user before planning

Do not start writing after one prompt. Ask, then plan. Use the `AskUserQuestion` tool where the options are enumerable; ask in prose where they are not. **Cap the first round at 4 questions.** Offer a "just use your judgement" escape on each.

### 2.1 The must-ask four

1. **Finish line.** "When you close this document, what sentence should you be able to say out loud?" This single answer determines chapter count, depth, and where you stop. Push for something testable — "explain how a VM in a pod ends up owning a slice of a NIC" beats "understand SR-IOV".
2. **Starting point.** "What do you already know well, and what is genuinely new?" This sets the bootstrap vocabulary — the words you may use without ceremony. Get it wrong in either direction and the document is useless: too basic and it insults, too advanced and it loses them in chapter 1.
3. **Depth floor.** "How deep do you want to go at the bottom layer — conceptual, or do you want to read real register/protocol/syntax detail?" Then hold that line all document.
4. **Implementation bias.** "Any specific vendor, library, distro, version, or codebase this should be biased towards?" A dossier that names real tools beats a vendor-neutral one, as long as you separate *universal architecture* from *tooling that drifts*.

### 2.2 Ask if not obvious

- **Direction.** Bottom-up (silicon → cluster) or top-down (user-visible symptom → root mechanism)? Bottom-up suits "how does this work"; top-down suits "why does this break".
- **Hands-on.** Does the reader have the hardware/environment to run commands? If yes, sidebars become "Try it". If no, sidebars become "What you'd see".
- **Budget.** Reading time target. Default ~90 minutes / ~15k words if unstated.
- **Format.** Single HTML (default) vs one markdown file per chapter. Only ask if they didn't say.
- **Existing pain.** "Is there a specific thing that already confused you?" Whatever they name gets its own diagram, guaranteed.

### 2.3 Then present the plan and stop

Before writing a word of prose, present a compact plan and wait for approval:

- deliverable shape (file path, tech, features)
- the spine, in one sentence
- a table: chapter | title | **terms introduced** | diagrams planned | references to verify
- process notes and the cut-lever ("if I must trim, I trim X, never the vocabulary discipline or diagrams")
- explicit assumptions

The terms column is the important one. It is where you catch dependency violations before they cost you a rewrite.

---

## 3. Phase 1 — Verify references before writing

Batch web searches for every planned reference, in parallel, before chapter 1. Prefer primary sources in this order:

1. Official project/kernel/standard documentation (`docs.kernel.org`, `kubernetes.io`, RFCs, vendor doc sites)
2. Maintainer-written material (blog posts, conference talks by the person who wrote the code)
3. Vendor whitepapers and teaching datasheets
4. Upstream repository READMEs and design docs
5. Good third-party explainers — last resort, and only if nothing above covers it

Reject: SEO blog spam, content farms, anything you cannot date, anything that contradicts a primary source.

Record for each: exact URL, exact title, and *why and when to read it* — that annotation is the value ("read the SR-IOV chapter only", "skim for the diagrams", "read after you've tried it once and failed").

Tag each with a layer tag. Layer tags are topic-specific — invent 4–6 for the topic and use them consistently: `[HW] [KERNEL] [VIRT] [K8S] [KUBEVIRT]`, or `[PROTOCOL] [CRYPTO] [SERVER] [CLIENT]`, or `[SQL] [PLANNER] [STORAGE] [REPLICATION]`.

---

## 4. Phase 2 — Writing rules

### 4.1 Voice

- Direct address or first-person-plural. "Let us be concrete about the danger." "You have seen it."
- **Simple, spoken English.** Short sentences. Assume a non-native reader with strong technical skills. Prefer the plain word: *use* not *utilise*, *stop* not *cease*, *build* not *construct*.
- Short paragraphs — 2 to 5 sentences. White space is a feature.
- Occasional dry humour, never breathless. "This is wonderful for performance and terrifying for security."
- Prose first. Bullet lists only for genuinely enumerable things: commands, checklists, knob tables.

**Banned words and moves** (they read as machine-written): *leverage, delve, seamless, robust, cutting-edge, in today's world, it's important to note, smoking gun, leak (as metaphor), unlock, supercharge, game-changer, journey (except the literal spine), dive deep, at the end of the day.* Also banned: three-item lists of adjectives, sentences that restate the previous sentence, and paragraphs that only announce what the next paragraph will say.

### 4.2 Techniques that make this format work

Steal these deliberately, they are what the reference implementation does:

- **Say what you are skipping, and why.** "This chapter skips lanes, link training, and electrical anything. All real, all irrelevant here." Readers relax when the scope is fenced.
- **Name the payoff sentence.** Once per document, state the single sentence the whole thing exists to make comprehensible — and place it where every word in it is finally defined. Bold it.
- **Repeat the same fact in three registers.** Prose, then diagram, then a one-line recap. Repetition across formats is not padding.
- **Foreshadow mechanically, not verbally.** Instead of "remember this for later", make chapter N's fact do visible work in chapter N+3, and point back: "this is diagram 2.1 made real".
- **The absence list.** When something is fast because of what it *doesn't* do, enumerate the skipped layers explicitly. That list is often the most valuable paragraph in the document.
- **Table for the knob list.** When there are 4+ settings, a two-column *knob → what it actually does in the hardware/system* table beats six paragraphs.
- **Separate stable from drifting.** One table per volatile chapter: "universal architecture" vs "vendor/version specifics". Date the volatile side ("as of 2026").
- **Analogies, one per concept, then drop them.** "The PF owns the flat, the VFs are tenants with keys to one room each." Do not extend an analogy past its first paragraph.

### 4.3 Chapter anatomy

Every chapter, in this order:

1. `Chapter N` label + title
2. **Metadata line**: reading time · layer tag(s) · **the spine sentence** locating the reader
3. Body: 900–1,600 words, with 2–4 `<h3>` sections
4. **≥1 diagram** with caption, placed *after* the prose that defines its vocabulary
5. **≤1 sidebar** — "Try it" (hands-on) or "What you'd see" (no hardware). Clearly skippable.
6. **Recap** — 3–5 sentences, dense, no new information
7. **New words in this chapter** — term → one-line definition, definition-list format
8. **Check yourself** — 2–3 questions answerable *only* by someone who read the chapter, **each shipping its answer behind a toggle**. Not trivia; make at least one a "trace the path" or "predict the value" question. Answers are specified in §4.5 and they are not optional.
9. **Go deeper** — 2–5 references with layer tag and why/when annotation

Chapter 0 is a prologue that frames the problem and its tension, and introduces the minimum vocabulary. The last chapter is an epilogue: honest limitations, sharp edges, 2-paragraph previews of where the field went next, then the merged glossary and master reference list.

### 4.4 Depth guardrail

Describe structures **functionally**, never as offset tables. "A counter the driver reads to see dropped spoofed packets" — yes. A register map — no. If you are transcribing a datasheet or an RFC's ABNF, you have gone too deep; the reader has the reference link for that.

Same rule for code: show the shortest realistic command or config that proves the point. Label illustrative transcripts as illustrative.

### 4.5 Check-yourself answers

A question with no answer is a rhetorical question. The reader cannot tell a confident wrong answer from a right one, which is the exact failure the block exists to catch. **Every question ships with its answer.**

**Mechanism: one `<details>` directly under each question.** Collapsed by default, so answering first is the path of least resistance and opening is a deliberate click. `<details>` is native — the toggle needs no JavaScript. An answers-section at the end of the document is the weaker alternative: it costs a scroll each way, so readers skip it and the questions go unanswered. Use the end-section only if the questions are genuinely exam-shaped and meant to be done in one sitting.

**What an answer must contain**, in this order:

1. The answer itself, flat, in the first clause. No throat-clearing, no restating the question.
2. The mechanism — *why* — named, in one or two sentences.
3. Where to check it: `file:line`, a command, a field name, or the diagram number.
4. For "predict the value": the actual number **and** the arithmetic that produced it.
5. The near-miss, when there is an obvious one — name the plausible wrong answer and say what is wrong with it. In most answers this is the highest-value sentence, because it is the only part the reader could not have written themselves.

Length 40–150 words. An answer longer than the paragraph that taught it means the chapter is missing something; fix the chapter, not the answer.

**Do not** write an answer that restates the question in the affirmative ("Yes, because it is designed that way"), and do not write "as covered above". If the honest answer is just a pointer, the question was trivia — replace the question.

**A question whose answer you have to look up in the code is a good question.** Look it up. Answers written from memory of your own prose are how a wrong claim survives into the quiz, where it does the most damage.

Markup and CSS:

```html
<div class="check">
  <h4>Check yourself</h4>
  <p>1. Predict the value: …</p>
  <details class="ans"><summary>Answer</summary>
    <p>256 nodes; the 257th fails. …</p>
    <p>The failure shows up in <code>status.ipam.operator-status</code> …</p>
  </details>
  <p>2. …</p>
  <details class="ans"><summary>Answer</summary>…</details>
</div>
```

```css
details.ans{margin:.35rem 0 1rem;background:var(--panel);border:1px solid var(--line);border-radius:8px;padding:.5rem .85rem}
details.ans>summary{cursor:pointer;font-size:.72rem;letter-spacing:.11em;text-transform:uppercase;color:var(--accent);font-weight:700;list-style:none}
details.ans>summary::-webkit-details-marker{display:none}
details.ans>summary::before{content:"\25B8  "}
details.ans[open]>summary::before{content:"\25BE  "}
details.ans[open]>summary{padding-bottom:.4rem;margin-bottom:.2rem;border-bottom:1px solid var(--line)}
details.ans p{margin:.55rem 0;font-size:.9rem}
details.ans p:last-child{margin-bottom:.15rem}
#answerctl{font:inherit;cursor:pointer;border:1px solid var(--line);background:#fff;border-radius:999px;padding:.25rem .7rem}
```

**Printing and bulk review.** A collapsed `<details>` does not print, so force them open first, and ship one control for readers who want to review rather than test:

```js
const ctl=document.getElementById('answerctl');
if(ctl){ctl.addEventListener('click',()=>{
  const all=[...document.querySelectorAll('details.ans')];
  const open=all.every(d=>d.open);
  all.forEach(d=>d.open=!open);
  ctl.textContent = open ? 'Open all answers' : 'Close all answers';
});}
window.addEventListener('beforeprint',()=>document.querySelectorAll('details.ans').forEach(d=>d.open=true));
```

---

## 5. Phase 3 — Diagrams

### 5.1 Rules

- Mermaid, in `<div class="mermaid">`. The div's text *is* the source, so it stays readable if the CDN fails — do not duplicate the source in a `<details>` block, it doubles the file for nothing.
- **A diagram must never contain a term the text has not yet defined.** Audit labels the same way you audit prose.
- Use the same bolded vocabulary as the text — identical words, not synonyms.
- Every diagram gets a caption starting with **`Diagram N.M — <what it is>.`** then "Notice ..." — tell the reader what to look at. A caption that only restates the title is wasted.
- Colour carries meaning: highlight the one box that matters (`style X fill:#e8f1fb,stroke:#0b6bcb,stroke-width:2px`), red for the dangerous/slow path, green for the safe/fast one.
- Distinguish **setup vs runtime** with line weight: dotted `-.->` for one-time control flow, thick `==>` for per-request/per-packet data flow. This single trick explains more than a paragraph.

### 5.2 Choosing the type

| Need | Use |
|---|---|
| Compare 2–4 approaches side by side | `flowchart LR` with one `subgraph` per approach |
| Layered structure, who contains whom | `flowchart TB` with nested subgraphs |
| Ordered exchange between parties | `sequenceDiagram` |
| Ordered exchange with a decision | `sequenceDiagram` + `alt / else / end` |
| The full-stack tower (once, near the end) | `flowchart TB`, reusing box names from earlier diagrams |
| The final walk-through of one operation | `sequenceDiagram`, with a `Note over` marking what is bypassed |

Plan ~1.5 diagrams per chapter and always include: (a) the "three approaches compared" diagram in chapter 0 or 1, (b) the core structural block diagram, (c) a protocol/negotiation sequence diagram, (d) a full-stack tower, (e) a final end-to-end walk.

### 5.3 Mermaid gotchas that will bite you

- **Quote every label**: `A["text here"]`. Unquoted parentheses, colons and slashes break the parse.
- **No `&`, `<`, `>`** anywhere in a label — the browser eats them as HTML. Write "and", "under", "over".
- **No parentheses in `sequenceDiagram` participant aliases.** `participant D as Driver, host or guest` — not `Driver (host or guest)`.
- `<br/>` works inside labels and is the right way to wrap.
- Edge labels: `A -->|"text"| B`. Keep them short; long ones wreck the layout.
- Node shapes: `["box"]`, `(["stadium"])`, `{"diamond"}`, `{{"hexagon"}}`. Hexagon reads well for "the thing that decides".
- Subgraph-to-subgraph arrows are legal and useful for spectrum/progression diagrams.
- Check every block starts with `flowchart`/`sequenceDiagram`/`graph` — a stray blank first line kills it silently.

---

## 6. Phase 4 — The HTML build

Single file. No build step. Everything inline except the Mermaid CDN import.

### 6.1 Skeleton

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>TITLE — a SUBJECT dossier</title>
<style>/* see 6.2 */</style>
</head>
<body>
<header class="hero">
  <div class="hero-in">
    <h1>Title</h1>
    <p class="sub">One-sentence promise of what the reader will be able to do.</p>
    <p class="meta">N chapters · ~X words · ~Y minutes · Z diagrams · written MONTH YEAR</p>
  </div>
</header>
<div class="wrap">
  <nav id="toc">…sticky list, each entry with reading time and layer tag…</nav>
  <main>
    <section id="how-to-read" class="card">…two reading paths + layer filter buttons…</section>
    <section id="ch0" class="chapter">…</section>
    …
    <section id="glossary" class="card">…merged, grouped by chapter…</section>
    <section id="references" class="card">…grouped by layer tag…</section>
    <!--APPEND-->
  </main>
</div>
<footer>…</footer>
<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
  mermaid.initialize({startOnLoad:true, theme:'neutral', flowchart:{htmlLabels:true, curve:'basis'}, securityLevel:'loose'});
</script>
<script>
  /* plus the two answer-toggle handlers from 4.5: the reveal-all control
     and the beforeprint hook. Both are required; a collapsed <details>
     does not print. */
  document.querySelectorAll('#filters button').forEach(b=>{
    b.addEventListener('click',()=>{
      b.classList.toggle('off');
      const off=[...document.querySelectorAll('#filters button.off')].map(x=>x.dataset.l);
      document.querySelectorAll('li.ref').forEach(li=>{
        li.style.display = off.includes(li.dataset.layer) ? 'none' : '';
      });
    });
  });
</script>
</body>
</html>
```

**Build it incrementally.** Write the skeleton with `<!--APPEND-->` before `</main>`, then add 1–2 chapters per edit by replacing that marker with `newChapters + <!--APPEND-->`. Remove the marker at the end. Do not try to emit 20k words in one tool call — a truncated write costs more to repair than three clean ones.

### 6.2 Stylesheet

Copy this wholesale; it is tuned and it prints well. It does **not** include the answer-toggle rules — those live with their markup in §4.5, and you need both.

```css
:root{
  --ink:#1b1f24; --ink-soft:#4a5560; --line:#dfe4ea; --bg:#fbfcfd; --panel:#fff;
  --accent:#0b6bcb; --accent-soft:#e8f1fb; --warm:#b45309; --warm-soft:#fdf3e3;
  --good:#0f7b6c; --good-soft:#e6f4f1; --code-bg:#f4f6f8;
  /* one colour per layer tag */
  --hw:#7c3aed; --kernel:#0b6bcb; --virt:#0f7b6c; --k8s:#b45309; --kubevirt:#be123c;
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
#toc .num{color:var(--ink-soft);font-variant-numeric:tabular-nums;margin-right:.4rem}
#toc .rt{display:block;font-size:.72rem;color:var(--ink-soft)}
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
aside.tryit{background:var(--warm-soft);border:1px solid #f0d9ae;border-left:4px solid var(--warm);border-radius:10px;padding:1rem 1.2rem;margin:1.6rem 0;font-size:.93rem}
aside.tryit h4{margin:0 0 .5rem;font-size:.78rem;letter-spacing:.12em;text-transform:uppercase;color:var(--warm)}
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
.refs ul{list-style:none;margin:0;padding:0}
.refs li.ref{padding:.5rem 0;border-top:1px solid var(--line)}
.tag{display:inline-block;font-size:.66rem;font-weight:700;letter-spacing:.08em;padding:.12rem .45rem;border-radius:4px;color:#fff;margin-right:.45rem;vertical-align:.08em}
.tag.HW{background:var(--hw)} .tag.KERNEL{background:var(--kernel)} .tag.VIRT{background:var(--virt)}
.tag.K8S{background:var(--k8s)} .tag.KUBEVIRT{background:var(--kubevirt)}
.note{font-size:.85rem;color:var(--ink-soft);font-style:italic}
table{border-collapse:collapse;width:100%;margin:1.4rem 0;font-size:.88rem}
th,td{border:1px solid var(--line);padding:.5rem .65rem;text-align:left;vertical-align:top}
th{background:var(--code-bg);font-size:.8rem}
#filters button{font:inherit;cursor:pointer;border:1px solid var(--line);background:#fff;border-radius:999px;padding:.25rem .7rem;margin:.15rem .25rem .15rem 0}
#filters button.off{opacity:.35}
.path{display:grid;grid-template-columns:1fr 1fr;gap:1rem;margin:1rem 0}
@media(max-width:640px){.path{grid-template-columns:1fr}}
.path div{border:1px solid var(--line);border-radius:10px;padding:.9rem 1rem;font-size:.9rem}
footer{border-top:1px solid var(--line);padding:2rem 1.5rem;text-align:center;color:var(--ink-soft);font-size:.85rem}
@media print{.hero{background:#fff;color:#000}#toc{display:none}.wrap{display:block}.chapter{border:none;padding:0;page-break-after:always}}
```

### 6.3 "How to read this" card

Always include it, always with two paths: **straight through** (the intended one) and **hop by layer tag** (the reference one). State the vocabulary rule there, so the reader knows every bold word was defined earlier and is searchable.

Also state, in this card, **how to use the Check-yourself blocks and how to get properly quizzed**. This paragraph is what makes the document work for a reader who is not the person who commissioned it — it travels with the file. Adapt this wording:

> **Check yourself, and how to use it.** Every chapter ends with 2–3 questions, and each one carries its answer behind a toggle. Answer out loud first, in full sentences, and only then open it — a question you can half-answer is the one worth re-reading the chapter for. If you would rather be quizzed properly, hand this file to an agent running the Alvar `probe` skill and ask it to quiz you chapter by chapter: it asks through a real question tool, scores your reasoning rather than your letter, and writes what it found to `.alvar/maps/`. `[Open all answers]`

Put the reveal-all button in the same paragraph.

### 6.4 Escaping

Code blocks containing HTML or XML must be entity-escaped (`&lt;`, `&gt;`, `&amp;`). YAML, shell and JSON usually need nothing. Check any `<` in a `<pre>` before you finish.

---

## 7. Phase 5 — Self-audit (mandatory, scripted)

Do not eyeball this. Run it.

```python
# audit.py — run from the directory containing the dossier
import re, html
from html.parser import HTMLParser

F = 'dossier.html'                      # <-- set
s = open(F, encoding='utf-8').read()
body = s[s.index('<main>'):s.index('</main>')]

# --- 1. structure counts -------------------------------------------------
print("figures", s.count('<figure class="dia">'), "closes", s.count('</figure>'),
      "mermaid", s.count('<div class="mermaid">'), "captions", s.count('<figcaption>'))
for b in ['chapter','recap','words','check','refs']:
    print(b, s.count('class="%s"' % b))
print("leftover APPEND markers:", s.count('<!--APPEND-->'))

# --- 1b. check-yourself answers: one <details> per question --------------
for i, blk in enumerate(re.findall(r'<div class="check">(.*?)</div>', s, re.S), 1):
    q = len(re.findall(r'<p>\d+\.\s', blk)); d = blk.count('<details')
    short = [len(re.sub('<[^>]+>','',a).split())
             for a in re.findall(r'<details class="ans">(.*?)</details>', blk, re.S)]
    flag = '' if q == d else '  !! %d questions, %d answers' % (q, d)
    thin = [n for n in short if n < 35]
    print('check block', i, 'q=%d a=%d' % (q, d), flag, ('thin answers: %s' % thin) if thin else '')
print("reveal-all control:", s.count('answerctl'), "| beforeprint hook:", s.count('beforeprint'))

# --- 2. mermaid sanity ---------------------------------------------------
for i, m in enumerate(re.findall(r'<div class="mermaid">\n(.*?)\n</div>', s, re.S), 1):
    head = m.strip().split('\n')[0]
    ok = head.startswith(('flowchart', 'sequenceDiagram', 'graph', 'stateDiagram'))
    ents = [c for c in ['&amp;', '&lt;', '&gt;'] if c in m]
    print(i, head[:30], 'OK' if ok else '!! BAD HEADER', 'entities:' + ','.join(ents) if ents else '')

# --- 3. HTML nesting -----------------------------------------------------
VOID = {'meta','br','hr','img','link','input','source'}
class P(HTMLParser):
    def __init__(self): super().__init__(); self.st=[]; self.err=[]
    def handle_startendtag(self,t,a): pass                  # self-closing: ignore
    def handle_starttag(self,t,a):
        if t not in VOID: self.st.append((t,self.getpos()))
    def handle_endtag(self,t):
        if t in VOID: return
        if not self.st: self.err.append(('extra close',t,self.getpos())); return
        if self.st[-1][0]!=t: self.err.append(('mismatch',t,'open:'+self.st[-1][0],self.getpos()))
        else: self.st.pop()
p = P(); p.feed(s)
print("unclosed:", p.st[:5], "errors:", p.err[:5])

# --- 4. vocabulary: no term used before its chapter ----------------------
# fill this from the plan's "terms introduced" column: term -> section id
TERMS = { 'example term': 'ch3' }
# cards written for someone who has already read the document may name anything:
# the what-changed card, the quiz revision card (4.5 / 8.3), and the back matter.
SKIP_SECTIONS = {'whats-new', 'whats-quiz', 'glossary', 'references', 'how-to-read'}
ids = [(m.start(), m.group(1)) for m in re.finditer(r'<section id="([\w-]+)"', body)]
order = [i for _, i in ids]
def chapter_of(pos):
    c = 'pre'
    for p_, n in ids:
        if p_ <= pos: c = n
    return c
for t, expect in TERMS.items():
    i = body.find(t)
    if i < 0: print("NOT FOUND:", t); continue
    c = chapter_of(i)
    if c in SKIP_SECTIONS: continue
    if c not in order or order.index(c) < order.index(expect):
        print("EARLY USE:", t, "in", c, "expected", expect, "|", body[max(0,i-70):i+40].replace('\n',' '))

# --- 5. spine + word counts ---------------------------------------------
for m in re.finditer(r'<section id="(ch\d+)"(.*?)(?=<section id="|\Z)', body, re.S):
    t = re.sub(r'<div class="mermaid">.*?</div>', '', m.group(2), flags=re.S)
    t = html.unescape(re.sub(r'<[^>]+>', ' ', t))
    print(m.group(1), len(t.split()), "words ->", max(1, round(len(t.split())/200)), "min")
```

Then fix, by hand:

- Every **EARLY USE** that is not a citation title. Rewrite in plain words or move the definition earlier.
- Any diagram label containing an undefined term — the script won't catch these, so re-read all labels once against the glossary.
- Reading times in the TOC and in each chapter's metadata line must match the measured word counts (÷200 wpm). **Patch them by line number, not by string replace** — repeated `"8 min · HW"` strings will replace each other's output and silently scramble.
- Confirm the spine sentence exists in all N metadata lines.

Report the audit result honestly in your final message, including anything you could not verify (e.g. "diagrams not visually rendered — no browser available").

---

## 8. Phase 6 — Probe again, after delivery

The document is a draft until the reader has hit it. Run this loop; do not wait to be asked.

### 8.1 Immediately after delivering

Ask, in one short message, no more than three of these:

1. "Read chapter 0 and 1 now, then tell me: which sentence was the first one you had to read twice?" — that sentence is a definition failure, fix it and everything downstream of it.
2. "Any diagram you had to look at more than twice?" — that diagram is doing two jobs; split it.
3. "Was the depth right at the bottom layer, or do you want more/less register-and-syntax detail?" — recalibrate the guardrail and re-edit the affected chapters.

### 8.2 Quiz them — through a real quiz tool, never pasted letters

The toggled answers let a reader check themselves. That is necessary and it is not sufficient: **nobody catches themselves being vague.** A reader who half-knows an answer opens the toggle, recognises it, and files the feeling as knowledge. The strongest signal available is an agent asking, scoring the *reasoning* rather than the letter, and writing down what it found.

Offer it explicitly, then actually do it: *"Want me to quiz you on chapters 1–4? Wherever you stumble, that chapter gets rewritten."*

#### Use the Alvar skills if they are installed

`probe` (find the edge of understanding), `teach` (one reasoning step per turn, quiz each step), and their companions `learn-visual` and `learn-verify`. Invoke them rather than improvising — they carry the scoring vocabulary and the file layout, and a second agent can pick the work up from the files they leave. `probe` before teaching anything; `teach` to close what the probe found.

If they are not installed, follow the protocol below inline. It is the same protocol; the skills are a packaging of it.

#### The one hard rule: never print A/B/C/D in the chat

Pasted multiple choice is not a quiz. The learner can scan all four options at once, pattern-match, and answer without retrieving anything — and you get no structured result to score. Call the harness's own question tool, which renders real choices and returns a selection.

| If the harness has | Call |
|---|---|
| `AskUserQuestion` | that — Claude Code |
| `ask_user_question` | that — Grok Build, Codex |
| `question` | that — OpenCode |
| `quiz`, else `ask_user` / `askUserQuestion` | that — Pi |

No match? Say which tool is missing and stop. Do not fall back to pasted letters.

#### Quiz shape

- **1–3 questions per call, then wait.** Not a twenty-question exam in one message.
- **One right answer**, single-select. Three content choices plus **"I don't know"**, always.
- **Do not mark the correct option "recommended", and do not habitually put it first.** Either leaks the answer. Shuffle the position across questions.
- Distractors must be wrong for a *reason*, not by wording. The best one encodes the misconception you actually expect — the component that does not exist, the field that is not checked, the step that does not happen.
- **Invite a talk-through.** The free-text / Other slot is where reasoning goes, and reasoning is the thing you are measuring.
- Start wide ("what kind of object is this?"), then split whichever strand the answer left ambiguous. Skip strands the reader has already demonstrated.

#### Scoring

| Result | Status |
|---|---|
| correct, with a sound reason | `known` |
| correct, thin or absent reason | `edge` |
| wrong, but a near miss | `edge` |
| wrong, foundation missing | `unknown` |
| "I don't know" | `blocked` |

A right letter with a wrong reason is `edge`, not `known`. That distinction is the entire value of the exercise; collapse it and you are back to self-marking.

After each batch, give a **one-line correction** for anything missed — one line, not a lecture. Teaching happens after the measurement, not during it.

#### Persist the result

Write `.alvar/maps/<slug>.md`: goal, a strand table (`strand | status | evidence`), and a quiz log (`Q1 [strand] <choice> — correct|wrong|idk — five words`). This is what makes the loop resumable and what lets you see, at the end, whether the gaps share a shape. They usually do, and naming that shape is worth more than the individual corrections.

#### Then fix the document, not the reader

A wrong or hesitant answer is a **defect in the chapter**. Rewrite the section, add a diagram, or add a sidebar with a worked example. Two specific moves:

- If the reader invented a component that does not exist, the chapter described an outcome without naming the mechanism that produces it. Name the mechanism.
- If they got the ownership right but the protocol wrong, the chapter said *who* and skipped *how*. That is the commonest gap this format produces, because ownership is easy to write and mechanism is not.

**And verify in the source before you teach the correction.** Quizzing pushes you into the mechanical detail the prose glossed, which is exactly where a claim that is true-but-loose survives. Read the code, then correct — several times a "correction" turns out to be sharper than what the chapter said, and that sharpening belongs back in the chapter.

### 8.3 Write the quiz back into the document

A quiz that only changes your notes is half-used. The document goes to other readers and none of them can see `.alvar/maps/`. So after a round, add a short dated **revision card** to the document itself, next to the "what changed" card. Keep it to 400–600 words; if it grows past that, the findings belong in the chapters, not in the card.

Two halves, and the second is the one that gets skipped.

**1. Which claims the quiz sharpened.** One line each, naming the chapter. Only list claims you re-read in the *source* — a quiz-driven correction written from memory of your own prose is how the loose claim survives a second time. And say plainly whether each one was *wrong* or *true-but-loose*, because that distinction tells the reader how much to distrust the rest of the document. In practice almost all of them are the second kind: a sentence that is correct at the altitude it was written and misleading the moment somebody needs the mechanism.

**2. What the quiz revealed about the reading, turned into a habit.** A score is not content. Look for the *shape* the misses share — they almost always share one — and write the shape rather than the tally. Then give the reader a single sentence they can carry, and point at the part of the document that serves it.

Rules that make the card worth its space:

- **Group the misses by framing, not by chapter.** The most useful finding this format has produced came from splitting questions into *"what is true?"* versus *"here is a symptom, what happened?"* — every recall question landed and almost every diagnostic one was answered by inventing a component that acts. Chapter-by-chapter scoring hides that completely, because the misses were spread across five chapters and looked like noise.
- **Write it about the document, never about the reader.** "This document failed to teach X" is better prose than "the reader did not know X", and it is also more accurate: a fact that is retrievable on demand but does not fire under pressure is a teaching failure, not a learning one. Never put a score or a name in the card.
- **Give the section its own id and add that id to the audit's vocabulary skip list**, alongside the what-changed card. A revision card is written for somebody who has already read the whole document, so it has to be free to name terms from any chapter. Skip this and the audit lights up, and you will be tempted to water the card down to keep it green.
- **Put it in the back-matter TOC group**, not among the chapters. It is not on the reading path.
- If a correction is big enough to change what a chapter teaches, **fix the chapter too** and let the card point at it. The card is a changelog, not a patch.

### 8.4 Standing offers to make

Name these explicitly so the user knows the format can grow:

- **Troubleshooting appendix** — "symptom → which chapter's mechanism is failing → command to confirm". Very high value once they start using the thing for real.
- **A hands-on lab chapter** — an ordered sequence of commands that builds the whole thing from nothing, cross-linked to the chapter that explains each step.
- **Extra chapters** for a side door you sign-posted but did not open.
- **A "what changed" refresh** for volatile chapters, dated, when the tooling moves.
- **A one-page cheat sheet** — the tower diagram, the command list, and the glossary, printable on one sheet.
- **Trim to a target** — if length overshot, offer the specific cuts rather than trimming silently.

### 8.5 On revision

- Keep the vocabulary audit green after every edit. Adding a paragraph to chapter 3 can break chapter 2's ordering.
- When you add a term, add it to the chapter's word list *and* the merged glossary. The glossary is grouped by chapter, so it must move with the text.
- Never renumber diagrams silently; captions are referenced in prose ("this is diagram 2.1 made real").

---

## 9. Failure modes to watch for

| Symptom | Cause | Fix |
|---|---|---|
| Reader lost in chapter 5 | A term slipped in undefined 2 chapters earlier | Run the audit; restructure, don't patch |
| Chapters read like separate articles | Spine sentence missing or generic | Rewrite each metadata line to reference the previous chapter's specific outcome |
| Diagram looks impressive, teaches nothing | It shows structure but not motion or fate | Add setup-vs-runtime line weights, or convert to a sequence diagram |
| Document feels like documentation | Claims are not grounded | Add a command, path, number or callout to every abstract paragraph |
| Content ages badly | Universal and version-specific facts are mixed | Split them into a table; date the volatile side |
| Over length | Breadth crept into side topics | Cut side topics; never cut vocabulary discipline, diagrams, or the recap blocks |
| Links rot / are wrong | Guessed URLs | Verify with web tools; name-without-URL when unverifiable |
| Truncated HTML mid-diagram | Tried to write too much per tool call | 1–2 chapters per edit, `<!--APPEND-->` marker pattern |
| Reader is confident and wrong | Check-yourself questions shipped without answers | §4.5 — every question gets a toggled answer, with the near-miss named |
| Quiz results feel good, retention does not | Pasted A/B/C/D; reader pattern-matched instead of retrieving | §8.2 — use the harness question tool, score the reason not the letter |
| Reader answers every fact and still cannot debug | Every question was recall-shaped; the document never asked "here is a symptom, what happened?" | Ask both framings, and record the split in the revision card (§8.3) |
| Quiz findings live only in the agent's notes | The document itself was never revised | §8.3 — a dated revision card in the file, not just a map under `.alvar/` |

---

## 10. Quick checklist

Before you say it is done:

- [ ] Every chapter: metadata line with spine sentence, ≥1 diagram + caption, recap, new words, check yourself, 2–5 verified refs
- [ ] **Every check-yourself question has a `<details class="ans">` answer** — counts match in the audit, none under ~35 words, each naming the mechanism and where to check it
- [ ] Reveal-all control present, `beforeprint` opens the toggles, how-to-read card explains how to use the questions and how to be quizzed
- [ ] Vocabulary audit script run, all early-uses resolved or justified as citation titles
- [ ] All diagram labels use only defined terms
- [ ] Mermaid blocks parse-checked, no `&`/`<`/`>`, no parens in participant aliases
- [ ] HTML nesting clean, no leftover `<!--APPEND-->`
- [ ] Reading times match measured word counts, in both TOC and chapter headers
- [ ] Merged glossary contains every per-chapter term
- [ ] Master reference list grouped by layer, filter buttons work
- [ ] Every URL verified this session; unverifiable ones named, not guessed
- [ ] Honest final report: word count vs target, anything unverified, deviations from spec
- [ ] Follow-up probe questions asked
- [ ] After a quiz round: dated revision card in the document (§8.3), its section id added to the audit's vocabulary skip list, and an entry in the back-matter TOC

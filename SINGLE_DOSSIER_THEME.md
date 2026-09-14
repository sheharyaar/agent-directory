# SINGLE_DOSSIER_THEME.md — the "field dossier" light theme

One self-contained HTML file. Light only. Paper-white background, ink-black text,
six muted categorical colours, Archivo + IBM Plex Mono. Print-safe.

Use this when the deliverable is **one dense technical document** — an audit, a comparison,
a packet walk, a score card. It is the theme, not the writing spec. For chaptered
learning documents with a narrative spine and Mermaid diagrams, see `AGENT.md`; that
file's §6.2 stylesheet (dark blue hero gradient) is the *older* look — do not mix them
in one file. Pick one.

**Reference implementations** (read one before starting; they are the shape you are aiming for):

| File | What it exercises |
|---|---|
| `../neverinstall/cilium/2026-08-10-aws-alignment-audit.html` | verdict stamps, score-card tables, hand-written SVG figures, example boxes, callouts, source-anchor table |
| `../neverinstall/cilium/2026-08-09-ebpf-datapath-dossier.html` | sticky table of contents, two-lane packet-flow figures built from HTML boxes (no SVG), monospace chips |

---

## 1. Head boilerplate

Two web fonts, nothing else. No framework, no JS, no build step.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>…</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Archivo:wght@400;500;700&family=IBM+Plex+Mono:wght@400;500&display=swap" rel="stylesheet">
<style> /* §2 tokens + §3 base + only the components you use */ </style>
</head>
```

Weights are deliberately only 400 / 500 / 700. There is no 600. If a rule says
`font-weight:620` it came from another theme — change it to 500 or 700.

---

## 2. Tokens — copy verbatim, do not re-pick colours

```css
:root{
  --bg:#FAFAF7; --ink:#20241F; --mut:#5C6157; --line:#DDE0D4; --card:#FFFFFF;
  --green-d:#3B6D11; --green-x:#27500A; --green-l:#EAF3DE;
  --teal-d:#0F6E56;  --teal-x:#085041;  --teal-l:#E1F5EE;
  --purple-d:#534AB7;--purple-x:#3C3489;--purple-l:#EEEDFE;
  --coral-d:#993C1D; --coral-x:#712B13; --coral-l:#FAECE7;
  --amber-d:#854F0B; --amber-x:#633806; --amber-l:#FAEEDA;
  --gray-d:#5F5E5A;  --gray-x:#444441;  --gray-l:#F1EFE8;
  --mono:'IBM Plex Mono',ui-monospace,monospace;
  --sans:'Archivo',system-ui,sans-serif;
}
```

### The three-step rule

Every colour is a triple and each step has exactly one job. Mixing them up is the
fastest way to make the document look like a different theme.

| Step | Job | Never |
|---|---|---|
| `-l` (light) | fill: box background, chip background, SVG `fill` | text, borders |
| `-d` (dark) | line: 1px borders, 3px left rules, SVG `stroke`, small solid marks | large fills, body text |
| `-x` (extra dark) | text on the matching `-l` fill, SVG `text` fill | backgrounds |

`-x` on `-l` clears WCAG AA at 14px. `-d` on `-l` does **not** always — so `-d` stays
on lines and out of prose. Neutral text is `--ink` for body, `--mut` for anything
secondary (ledes, captions, table headers, metadata).

### Semantic assignment

Fix the meaning once per document and put it in a legend. These are the assignments
the two reference files use:

| Colour | Audit meaning | Datapath meaning |
|---|---|---|
| green | matches the thing we are compared against | — |
| teal | same result, different machine; also links and example boxes | — |
| purple | different on purpose; the carried-VPC idea | the new datapath (eBPF) |
| coral | a gap, and the network edge | the old datapath (legacy) |
| amber | not built yet; warnings and callouts | — |
| gray | neutral, out of scope, the guest's own stack | unclassified lane |

**Two categorical colours per figure, three at the very most.** A figure that needs
four is two figures.

---

## 3. Base — always include

```css
*{box-sizing:border-box}
html{scroll-behavior:smooth}
body{margin:0;background:var(--bg);color:var(--ink);font-family:var(--sans);
     font-size:16px;line-height:1.65;-webkit-font-smoothing:antialiased}
a{color:var(--teal-d);text-decoration-thickness:1px;text-underline-offset:3px}
a:focus-visible{outline:2px solid var(--teal-d);outline-offset:2px;border-radius:2px}
code{font-family:var(--mono);font-size:.88em;background:var(--gray-l);padding:1px 5px;border-radius:3px}

.wrap{max-width:920px;margin:0 auto;padding:56px 28px 110px}

/* headings — h2 is the biggest thing after h1. Do not let h3 grow past it. */
h1{font-size:clamp(28px,5vw,44px);font-weight:700;letter-spacing:-.02em;line-height:1.08;margin:10px 0 14px}
h2{font-size:24px;font-weight:700;letter-spacing:-.01em;margin:0}
h3{font-size:17px;font-weight:500;margin:30px 0 6px}
h4{font-size:15px;font-weight:500;margin:22px 0 6px}
p{margin:12px 0;max-width:76ch}
.lede{color:var(--mut)}

ul.tight{margin:12px 0;padding-left:22px}  ul.tight li{margin:8px 0}
ol.tight{margin:12px 0;padding-left:24px}  ol.tight li{margin:10px 0}

@media print{ figure,table{break-inside:avoid} body{background:#fff} }
@media (max-width:600px){ td,th{font-size:12.5px} }
```

`--wrap` is 920px for prose. Raise it to **1180px only** if the document contains
wide horizontal figures (the packet-flow lanes need it); text blocks stay capped in
`ch` regardless, so the measure does not change.

---

## 4. Components

Take the ones you need. Each is markup + CSS together; do not ship CSS for a
component the document does not use.

### 4.1 Document header and eyebrow

The eyebrow is the theme's signature: monospace, uppercase, wide letter-spacing,
muted. It labels the header, every callout, and every example box.

```css
header.doc{border-bottom:2px solid var(--ink);padding-bottom:26px;margin-bottom:14px}
.eyebrow{font-family:var(--mono);font-size:12px;letter-spacing:.14em;text-transform:uppercase;color:var(--mut)}
.meta{display:flex;flex-wrap:wrap;gap:8px 22px;font-family:var(--mono);font-size:12px;color:var(--mut)}
.meta strong{color:var(--ink);font-weight:500}
```

```html
<header class="doc">
  <div class="eyebrow">Field dossier · VPC-ALIGN / 2026-08</div>
  <h1>Our VPC against AWS:<br>what matches, what does not, and why</h1>
  <div class="meta">
    <span><strong>Compiled</strong> 10 Aug 2026</span>
    <span><strong>Branch</strong> feat/overlapping-cidr-multitenancy</span>
    <span><strong>Reader</strong> anyone; no AWS background needed</span>
  </div>
</header>
```

If the metadata is one running line with `·` separators instead of separate spans,
drop the `display:flex` — flex turns each text node into its own item and scrambles
the order. Use `font-family:var(--mono);font-size:12px;line-height:1.9` instead.

### 4.2 Section head

```css
section{margin:0 0 64px}
.sechead{display:flex;flex-wrap:wrap;align-items:baseline;gap:12px;border-top:1px solid var(--line);padding-top:26px}
.secnum{font-family:var(--mono);font-size:13px;color:var(--mut);letter-spacing:.12em}
section h2{flex:1 1 auto}
```

```html
<section id="s03">
  <div class="sechead"><span class="secnum">§ 03</span><h2>Score card</h2></div>
  <p class="lede">Read the verdict first; the sections after this one explain the interesting rows.</p>
```

Without `.sechead`, put the rule on the heading itself:
`h2{border-top:1px solid var(--line);padding-top:26px;margin:64px 0 10px}`.

### 4.3 Verdict stamps

A pill, light fill, dark border, extra-dark text. This is how a score card is read
at a glance. Declare every stamp once in a legend under the header.

```css
.stamp{display:inline-block;font-family:var(--mono);font-size:11px;letter-spacing:.08em;
       text-transform:uppercase;padding:3px 10px;border-radius:999px;border:1px solid;
       background:var(--card);white-space:nowrap;vertical-align:middle}
.stamp.same     {color:var(--green-x); border-color:var(--green-d); background:var(--green-l)}
.stamp.close    {color:var(--teal-x);  border-color:var(--teal-d);  background:var(--teal-l)}
.stamp.onpurpose{color:var(--purple-x);border-color:var(--purple-d);background:var(--purple-l)}
.stamp.gap      {color:var(--coral-x); border-color:var(--coral-d); background:var(--coral-l)}
.stamp.missing  {color:var(--amber-x); border-color:var(--amber-d); background:var(--amber-l)}
.stamp.more     {color:var(--gray-x);  border-color:var(--gray-d);  background:var(--gray-l)}

.legend{display:flex;flex-wrap:wrap;gap:10px;align-items:center;padding:16px 0 4px;
        border-bottom:1px solid var(--line);margin-bottom:44px}
.legend .eyebrow{margin-right:6px}
```

Stamps sit in a table cell, or after an `h3` (`section h3 .stamp{margin-left:8px}`).
The label inside the stamp may be shorter than the legend entry — `Same` for
`Same as AWS` — and may carry an id: `<span class="stamp gap">Gap · D4</span>`.

### 4.4 Tables

Monospace uppercase headers, a 2px ink rule under them, hairline rows, no zebra, no
vertical lines, no outer border. Set column widths on `th` so numbers line up.

```css
table{width:100%;border-collapse:collapse;margin:20px 0;font-size:14px}
th{font-family:var(--mono);font-size:11px;letter-spacing:.09em;text-transform:uppercase;color:var(--mut);
   text-align:left;padding:8px 12px 8px 0;border-bottom:2px solid var(--ink);vertical-align:bottom}
td{padding:10px 12px 10px 0;border-bottom:1px solid var(--line);vertical-align:top}
td:last-child,th:last-child{padding-right:0}
.src{font-family:var(--mono);font-size:11.5px;color:var(--mut)}   /* file:line cells */
```

Close the document with a source-anchor table: claim on the left, `file:line` on the
right in `.src`. It is what makes the document checkable.

### 4.5 Figure shell

```css
figure{margin:26px 0;background:var(--card);border:1px solid var(--line);border-radius:12px;padding:22px 22px 14px}
figure svg{display:block;width:100%;height:auto}
figcaption{font-family:var(--mono);font-size:12px;color:var(--mut);
           border-top:1px dashed var(--line);margin-top:16px;padding-top:10px;line-height:1.6}
```

Caption goes **below** the figure, numbered, and says what the reader should take
away — not what the picture contains: *"Fig. 1 — Almost every difference traces back
to one line."*

If the caption has to lead (it is the figure's title), flip it: caption in sans
15px/500, then a monospace sub-line carrying the dashed rule as `border-bottom`.

### 4.6 Example box and callout

Two left-ruled blocks, and they mean different things. `.ex` is a worked example —
neutral, card background, teal rule. `.callout` is a warning — amber fill, amber
rule, amber text throughout.

```css
.ex{background:var(--card);border:1px solid var(--line);border-left:3px solid var(--teal-d);
    border-radius:0 8px 8px 0;padding:14px 20px;margin:18px 0}
.ex .eyebrow{color:var(--teal-x)}
.ex p{margin:8px 0}
.ex table{margin:10px 0 4px;font-size:13.5px}
.ex th{border-bottom-width:1px}

.callout{background:var(--amber-l);border-left:3px solid var(--amber-d);padding:16px 20px;margin:26px 0}
.callout .eyebrow{color:var(--amber-x)}
.callout p{margin:8px 0 0;color:var(--amber-x)}
```

```html
<div class="ex">
  <div class="eyebrow">Example — the rule everyone writes first</div>
  <p>A tenant whose VPC is <code>10.64.0.0/16</code> writes: <em>allow TCP 80 from <code>10.64.0.0/16</code></em>.</p>
  <table>
    <tr><td style="width:12%"><strong>AWS</strong></td><td>Every instance in the range can now reach port 80.</td></tr>
    <tr><td><strong>Ours</strong></td><td>The object is refused. Reason: <code>CIDRNamesTheVPCsOwnRange</code>.</td></tr>
  </table>
</div>
```

A headerless two-column table inside `.ex` is the standard side-by-side. First cell
carries the width.

### 4.7 Sticky table of contents

For long documents. Monospace, one horizontal scrolling line, sticky at the top.

```css
nav.toc{position:sticky;top:0;z-index:20;background:var(--bg);border-bottom:1px solid var(--line);
        padding:12px 0;margin-bottom:44px;font-family:var(--mono);font-size:12px;letter-spacing:.04em}
nav.toc a{color:var(--mut);text-decoration:none;margin-right:20px;white-space:nowrap}
nav.toc a:hover{color:var(--teal-d)}
.toc-inner{max-width:1180px;margin:0 auto;padding:0 28px;overflow-x:auto;white-space:nowrap}
```

The bar spans the viewport; `.toc-inner` re-applies the wrap width. Keep both widths
equal to `.wrap`'s.

### 4.7b Side index rail

The wide-screen companion to 4.7: a fixed index in the left margin, so the reader
always sees the section map. Ship **both** navs — the media query decides which one
shows, so narrow screens keep the top bar and wide screens get the rail without two
indexes competing.

```css
nav.side{display:none}
@media (min-width:1360px){
  nav.toc{display:none}
  nav.side{display:block;position:fixed;top:96px;left:calc(50% - 460px - 208px);width:180px;
           font-family:var(--mono);font-size:11.5px;line-height:2;letter-spacing:.04em;
           border-left:2px solid var(--line);padding-left:14px}
  nav.side .eyebrow{margin-bottom:6px}
  nav.side a{display:block;color:var(--mut);text-decoration:none;white-space:nowrap;
             overflow:hidden;text-overflow:ellipsis}
  nav.side a:hover{color:var(--teal-d)}
  nav.side .n{color:var(--ink);font-weight:500;margin-right:6px}
}
@media print{ nav.side,nav.toc{display:none} }
```

```html
<nav class="side" aria-label="Section index">
  <div class="eyebrow">Index</div>
  <a href="#s01"><span class="n">01</span>Verdict</a>
  <a href="#s02"><span class="n">02</span>Why an object</a>
  <a href="#s04"><span class="n">04</span>Costs + AWS</a>
</nav>
```

Rules that make it sit right:

- The `left` offset is tied to the wrap width: `calc(50% - <wrap/2> - 208px)`.
  920px wrap → `- 460px`; 1180px wrap → `- 590px`. Change one, change the other.
- The 1360px breakpoint is the narrowest viewport where a 180px rail plus the 920px
  column still leaves a margin; widen it if the wrap is 1180px (use ~1620px).
- Labels stay short — two or three words, ellipsis handles overflow. The number
  span (`.n`) is ink at weight 500; the label stays muted until hover.
- No scroll-spy. Highlighting the current section needs JavaScript, and this theme
  ships none; the rail is a map, not a position marker. If a document genuinely
  needs spy highlighting, that is a recorded exception, not a default.

### 4.8 Footer

```css
footer{border-top:2px solid var(--ink);margin-top:20px;padding-top:16px;
       font-family:var(--mono);font-size:12px;color:var(--mut)}
```

One monospace paragraph: what revision this is, what changed, where the claims come
from, and what is not yet verified. It bookends the 2px ink rule under the header.

### 4.9 Lanes, hops and chips — the packet-walk figure

Comparison flows drawn as HTML boxes instead of SVG. Reflows on narrow screens,
text is selectable, and the numbers come from a CSS counter. Two lanes: one per
system, each in its categorical colour.

```css
/* pick the two slots once, name them, and use the aliases below */
:root{
  --ebpf:var(--purple-d);  --ebpf-x:var(--purple-x);  --ebpf-l:var(--purple-l);
  --legacy:var(--coral-d); --legacy-x:var(--coral-x); --legacy-l:var(--coral-l);
}

.lane{border:1px solid var(--line);border-left:3px solid var(--gray-d);border-radius:0 8px 8px 0;
      padding:12px 16px 14px;margin-bottom:14px;background:var(--gray-l)}
.lane.legacy{border-left-color:var(--legacy);background:var(--legacy-l)}
.lane.ebpf{border-left-color:var(--ebpf);background:var(--ebpf-l)}
.lane-title{font-family:var(--mono);font-size:11px;letter-spacing:.09em;text-transform:uppercase;
            color:var(--mut);margin-bottom:12px}
.lane.legacy .lane-title{color:var(--legacy-x)}
.lane.ebpf .lane-title{color:var(--ebpf-x)}
.lane-title .count{color:var(--mut);text-transform:none;letter-spacing:.02em}

.hops{display:flex;flex-wrap:wrap;align-items:stretch;gap:6px;counter-reset:hop}
.hop{counter-increment:hop;position:relative;flex:1 1 130px;min-width:128px;max-width:220px;
     background:var(--card);border:1px solid var(--line);border-radius:8px;
     padding:9px 11px 10px;font-size:12.5px;line-height:1.5}
.hop::before{content:counter(hop);position:absolute;top:-8px;left:-6px;width:18px;height:18px;
     border-radius:999px;font-family:var(--mono);font-size:10px;font-weight:500;
     display:flex;align-items:center;justify-content:center;
     background:var(--card);border:1px solid var(--gray-d);color:var(--gray-x)}
.lane.legacy .hop::before{border-color:var(--legacy);color:var(--legacy-x)}
.lane.ebpf .hop::before{border-color:var(--ebpf);color:var(--ebpf-x)}
.hop b{display:block;font-size:12.5px;font-weight:700;margin-bottom:3px}
.hop .fn{font-family:var(--mono);font-size:10.5px;color:var(--mut);display:block;margin-bottom:3px;word-break:break-all}
.arr{align-self:center;color:var(--mut);font-size:15px;flex:0 0 auto;padding:0 1px}

.chips{display:flex;flex-wrap:wrap;gap:3px;margin-top:6px}
.chip{font-family:var(--mono);font-size:10px;line-height:1.4;background:var(--card);
      border:1px solid var(--line);border-radius:999px;padding:1px 8px;color:var(--mut);word-break:break-all}
.chip.map{color:var(--ebpf-x);border-color:var(--ebpf);background:var(--ebpf-l)}
.lane.legacy .chip.map{color:var(--legacy-x);border-color:var(--legacy);background:var(--legacy-l)}

.skips{font-size:13px;color:var(--mut);border-top:1px dashed var(--line);padding-top:12px;margin-top:2px}
.skips .chip{text-decoration:line-through;background:var(--gray-l)}
.wirebreak{text-align:center;font-family:var(--mono);font-size:11px;color:var(--mut);
           letter-spacing:.14em;text-transform:uppercase;margin:2px 0 10px}
```

```html
<figure class="flow">
  <figcaption>Pod→Pod, same node, via ClusterIP</figcaption>
  <div class="figsub">First packet of a TCP flow. Later packets skip the NAT walk.</div>
  <div class="legend">
    <span class="key"><span class="swatch legacy"></span>Legacy — kube-proxy (iptables)</span>
    <span class="key"><span class="swatch ebpf"></span>Cilium eBPF</span>
  </div>
  <div class="lane ebpf">
    <div class="lane-title">Cilium eBPF path <span class="count">— 7 hops</span></div>
    <div class="hops">
      <div class="hop"><b>App syscall</b><span class="fn">cil_sock4_connect · bpf_sock.c:446</span>rewrites ClusterIP at connect()<div class="chips"><span class="chip map">cilium_lb4_services_v2</span></div></div>
      <div class="arr">→</div>
      <div class="hop"><b>Dest pod</b>client address intact</div>
    </div>
  </div>
  <div class="wirebreak">— wire —</div>
  <div class="skips">Stages this lane never enters: <span class="chip">nf_conntrack</span></div>
</figure>
```

Legend swatches match the lane: light fill, dark 1px border, never a solid block.

```css
.legend .swatch{width:12px;height:12px;border-radius:3px;display:inline-block;border:1px solid}
.legend .swatch.ebpf{background:var(--ebpf-l);border-color:var(--ebpf)}
.legend .swatch.legacy{background:var(--legacy-l);border-color:var(--legacy)}
```

Do not put `white-space:nowrap` on `.chip` — several chips hold whole sentences and
will overflow the card. `word-break:break-all` alone is correct.

---

## 5. SVG diagrams

Hand-written inline SVG. No diagram library, no Mermaid in this theme.

```css
svg text{font-family:var(--sans)}
.st{font-size:14px;font-weight:500}     /* box title  */
.ss{font-size:11.5px;font-weight:400}   /* box detail */
```

Conventions, all visible in the audit's Fig. 1 and Fig. 2:

- `viewBox="0 0 680 …"`, width 100%, height auto. 680 wide is the standard column;
  keep every figure on it so line weights match across the document.
- `role="img"` plus a one-line `aria-label`, and a `<title>` as the first child.
- Boxes: `<rect rx="8">`, `fill` = a `-l` token, `stroke` = the matching `-d`,
  `stroke-width="0.75"`. Big banner boxes use `rx="10"`.
- Text: `-x` for the title, `-d` for the detail line. Always
  `text-anchor="middle" dominant-baseline="central"`.
- One arrowhead marker per figure, reused:
  ```html
  <defs><marker id="e1" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
    <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  </marker></defs>
  ```
  Connectors are `stroke="#5F5E5A"` (`--gray-d`) at `stroke-width` 1.3–1.4.
  `context-stroke` makes the head inherit the line's colour.
- Hex literals are unavoidable inside SVG attributes (`var()` does not work in
  presentation attributes). Copy the exact token values from §2 — never eyeball a
  near-miss shade.
- Two-column comparisons: left column centred on x=165, right on x=515, boxes 250
  wide at x=40 and x=390. Rows step 76px. A full-width summary bar at the bottom
  (`--gray-l`) carries the one sentence the reader must not miss.

---

## 6. Classes the reference files use but never define

The audit has four of these — inherited from an earlier draft. If you copy markup
from it, either define them or convert the markup. Definitions that fit the theme:

```css
.card.acc{background:var(--card);border:1px solid var(--line);border-left:3px solid var(--amber-d);
          border-radius:0 8px 8px 0;padding:16px 20px;margin:26px 0}
.card.acc h4{margin-top:0}
h5{font-family:var(--mono);font-size:11px;letter-spacing:.09em;text-transform:uppercase;
   color:var(--mut);margin:0 0 6px}
.cmp{display:grid;grid-template-columns:1fr 1fr;gap:18px;margin:18px 0}
.cmp>div{background:var(--card);border:1px solid var(--line);border-radius:8px;padding:14px 16px}
.cmp p{margin:0;font-size:14px}
.pill{display:inline-block;font-family:var(--mono);font-size:10.5px;letter-spacing:.08em;
      text-transform:uppercase;padding:2px 9px;border-radius:999px;border:1px solid}
.pill.p-done{color:var(--green-x);border-color:var(--green-d);background:var(--green-l)}
@media (max-width:600px){ .cmp{grid-template-columns:1fr} }
```

---

## 7. Rules

**Light only.** No `@media (prefers-color-scheme: dark)` block. A second surface
means every categorical colour needs a second validated step, and the figures'
hard-coded SVG hexes cannot follow. If a dark version is genuinely wanted, that is a
separate theme file, not a media query bolted onto this one.

**Never invent a colour.** Six triples plus five neutrals is the whole palette. If a
document needs a seventh category, the figure is doing too much.

**No emoji, no gradients, no shadows, no icon fonts.** Colour, weight, a hairline and
a monospace label carry every distinction in this theme.

**Every claim is anchored.** `<code>file.c:123-145</code>` inline, or a `.src` cell in
the closing table. A paragraph with no anchor is decoration.

**Say what is unverified.** Mark a claim not yet reproduced, in the text and again in
the footer. An audit is a dated statement: annotate rows rather than rewriting them,
and keep a "what moved since" block near the top.

**Prose.** Simple English. Banned words: *leak*, *hurt*, *bites*, *substrate* — write
"networking layer" or "CNI" for the last, "route import" for a route leak. Expand
every acronym on first use. End a research deliverable with sponsor-facing open
questions.

---

## 8. Checklist before shipping

- [ ] Fonts linked; no `font-weight` outside 400 / 500 / 700
- [ ] Tokens copied verbatim from §2; every `var()` resolves
- [ ] No `prefers-color-scheme` block anywhere
- [ ] `-l` fills, `-d` lines, `-x` text — no crossovers
- [ ] Legend declares every stamp and swatch the document uses
- [ ] `h2` (24px) is bigger than `h3` (17px) — the inversion is the commonest port bug
- [ ] SVG hexes match the tokens exactly; each figure has `role`, `aria-label`, `<title>`
- [ ] Every figure has a numbered caption saying the takeaway
- [ ] Closing source-anchor table present; unverified claims marked
- [ ] Long document: both navs shipped (4.7 top bar + 4.7b side rail); rail offset matches the wrap width; print hides both
- [ ] Print CSS and the 600px table rule included
- [ ] CSS braces balanced; no class in the markup left unstyled
  (`grep -o 'class="[^"]*"' file.html` against the stylesheet)

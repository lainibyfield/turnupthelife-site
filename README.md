<!-- COURSE TEMPLATE — LICENSABLE BLANK. Structure only, no course content.
     See "License" at the foot of this file before distributing or selling. -->

# Build a course in plain HTML

This is the codebase for turnupthelife.com — and it's written to be copied.

Static HTML. No build step, no framework, no npm, no database. Every file opens
in a browser and uploads as-is to any host. The files are commented far more
heavily than production code normally is, because they're meant to teach: open
any lesson file, follow the comments, and turn it into your own course.

Nothing here is clever. It's deliberately ordinary HTML, CSS, and vanilla
JavaScript, so that someone who has never touched a framework can own their own
site.

**This README is about the structure, not the content.** The teaching material
in these files is one person's method; the architecture underneath it will hold
any subject that has lessons, steps, and things a student needs to do in order.

---

## The mental model: chassis and cargo

Everything in this project is one of two things.

**The chassis** must be identical on every page — colours, fonts, the timer, the
sticky header, the shared warm-up. If these drift page to page, the site stops
feeling like one thing and starts feeling like a folder of unrelated documents.
Students notice, even when they can't say why.

**The cargo** is your actual teaching — lessons, the sections inside them, the
items inside those. Insert, delete, reorder, rewrite. The chassis exists so the
cargo can change without anything breaking.

The rest of this document is mostly about which is which.

---

## File map

| File | Role |
|---|---|
| `course.html` | **The shell.** Home, curriculum list, unlock codes. Contains **no lesson content** — it links out. |
| `trial-class.html` | The free sample *and* Lesson 1 — one file serving both, so they can't drift apart. |
| `lesson-2.html` … `lesson-7.html` | One lesson per file, standalone. |
| `kitchen.html`, `matrix.html`, `planner-builder.html` | A second, unrelated course sharing the same chassis — proof the structure travels. |
| `*.md` | Reference docs, not part of the site. |
| `template/` | **A blank skeleton to copy.** `index.html` + `lesson-1.html` with placeholder content and every pattern annotated. Start here. |

---

## Anatomy of a lesson file

Every lesson is the same five layers. Open one alongside this section — pick the
one closest to what you're building and copy it.

    1. <head>          meta tags, fonts, colour-scheme declaration
    2. <style>         :root design tokens           <- CHASSIS (frozen)
                       page styles                   <- chassis, edit with care
    3. <body>
         homebar       "back to the index" bar       <- chassis
         hero          title, promise, what's needed <- CARGO (yours)
         jump nav      sticky section buttons        <- chassis (ids must match)
         sections      the actual lesson             <- CARGO (yours)
         footer        print button + disclaimer     <- chassis
    4. <script>        nav, progress, tracking       <- chassis
                       timer JS                      <- CHASSIS (frozen)

The two frozen blocks carry a loud `DO NOT EDIT HERE — SHARED` banner in the
file itself.

---

## The chassis

### 1. The `:root` design tokens

Every colour and font on the site is a named variable declared once at the top
of each file — background, card, text, and a handful of accent colours mapped to
meaning (one for "go", one for "caution", one for "highlight").

**To re-skin the entire site, change those values.** That's the whole job.
Nothing else hard-codes a colour. If you find yourself typing a hex code
anywhere else, stop — add a token instead, or you've made something that won't
change when everything else does.

### 2. The timer JS

The countdown buttons and their phases. Identical across every lesson file.

Both blocks are **byte-identical across all lesson files**, verified by
checksum. Change one, change them all.

### Why duplicated instead of one shared file?

So each lesson works completely on its own. You can open, preview, email, or
hand off any single lesson file and it works with nothing else loaded. While
lessons are still being built and tested one at a time, that isolation is worth
more than the tidiness of a shared stylesheet.

**When to stop duplicating:** once the design is frozen and no new lessons are
coming, pull these blocks into `tokens.css` and `timer.js` and link them
everywhere. Do it *then* — the DRY win only beats the isolation win after the
churn stops.

---

## The cargo

### Three nesting levels

    SECTION      one block of the lesson
      +- CARD    one item (exercise, task, step)
           +- THE THREE QUESTIONS   do this / too difficult / too easy

Add, delete, or reorder freely. Two bookkeeping rules, below.

### Adding a section

Copy an existing `<section class="sec">` and change four things:

```html
<section class="sec" id="yourid">                       <!-- 1. unique id -->
  <div class="sec-head">
    <span class="stepno">Step 3 / 7</span>              <!-- 2. step number -->
    <span class="tag">BUILD</span>
    <h2>Your Title</h2>                                 <!-- 3. title -->
    <span class="sec-min">6-8 min</span>
  </div>
  <p class="sec-intro">One sentence on why this block exists.</p>
  <div class="timerbank">...</div>                      <!-- optional -->

  <!-- your cards go here -->

  <a class="upnext" href="#nextid" data-go="nextid">    <!-- 4. handoff -->
    <span class="un-k">Up next &middot; Step 4 of 7</span>
    <span class="un-t">Next Title &rarr;</span>
  </a>
</section>
```

Then add a matching button to the jump nav:

```html
<button data-go="yourid">Your Title</button>
```

**The rule: `data-go="x"` must match `id="x"`.** That's the only wiring. The nav
dots, the scrolling, and the section-complete highlight all key off it.

The `.stepno` badges and the "Step N of M" text are hand-written — nothing
counts them for you.

### Adding a card

```html
<article class="ex">
  <div class="ex-body">
    <div class="ex-top">
      <h3 class="ex-name">Item Name<span class="sub">one-line description</span></h3>
      <label class="donebox"><input type="checkbox" data-track="section-item">Done</label>
    </div>
    <p class="cue">One memorable sentence. This is what they'll actually remember.</p>
    <div class="data">
      <span class="chip"><span class="k">Label</span> value</span>
    </div>
    <ul class="cues">
      <li>Short instruction.</li>
    </ul>
    <div class="threeq">...</div>
    <a class="watch" href="...">Watch / read more</a>
  </div>
</article>
```

**`data-track` must be unique within the file.** It's the key the progress bar
counts and localStorage saves.

### The three questions

Every card answers the same three, in the same order, so nobody has to hunt:

```html
<div class="threeq">
  <span class="tq tq-do">  <span class="tq-k">Do this</span>...</span>
  <span class="tq tq-down"><span class="tq-k">If that's too difficult</span>...</span>
  <span class="tq tq-up">  <span class="tq-k">If that's too easy</span>...</span>
</div>
```

**One option per box.** Two options is a decision; one is an instruction, and
tired people follow instructions. This single pattern did more for these lessons
in testing than anything else in this file.

You can drop the third box where a task is genuinely self-limiting, or where
making it harder isn't the point.

### After any change: rebuild the tracking map

Near the bottom of each file:

```js
var blockMap = {"sectionid":['track-id-1','track-id-2'], ...};
```

This maps each section id to the `data-track` ids inside it, and drives the
progress bar and the section-complete dots. **It does not update itself.** Add
or remove a card and this must be edited to match.

---

## Building a new lesson from scratch

1. **Copy the closest existing lesson.** Never start from a blank file — you'll
   lose the chassis.
2. **Rename the file**; change `<title>` and the file-header comment.
3. **Change the storage key.** Search for `...-progress` and give the new lesson
   its own. **If you skip this, two lessons share one set of checkboxes** —
   ticking a box in one silently ticks it in the other. Easiest mistake to make,
   hardest to spot.
4. **Rewrite the hero** — title, promise, time, what they need.
5. **Replace the sections**; update the jump nav to match.
6. **Rebuild `blockMap`.**
7. **Wire it into the index** in `course.html`: add `data-standalone="file.html"`
   to its row so it flips from "Coming soon" to open when unlocked. Update the
   unlock-bar text too — it names what's ready and doesn't update itself.
8. **Open it and click everything.** Every nav button, every timer, every
   checkbox — then reload and confirm the ticks survived.

---

## Structural patterns worth stealing

These aren't code. They're the design decisions that make a course feel like a
curriculum instead of a pile of sessions.

### Phases with a guiding question

Group lessons into phases, and give each phase **one question it answers**. A
student should always be able to say not just what they're doing but why they're
in this part of the course. Phases also make natural paywall boundaries and
natural stopping points.

Gate phases by readiness, not by calendar. Some people move through a phase in a
week; most take longer. Nothing in the interface should imply a schedule someone
can fall behind.

### One big idea per lesson

Every item in a lesson is *evidence of that idea*, not a separate concept. A
lesson isn't seven things; it's one thing shown seven ways.

### Name the concept after the experience, not before

Let students *do* something across several lessons, then name and explain it.
The framework attaches to concrete memory instead of arriving as theory. This is
why "why it works" material belongs at the end of a curriculum, not the start.

**This operates at curriculum scale, not lesson scale.** The lesson where a
concept finally gets named will usually open by naming it — that isn't an
exception, it's what arriving looks like. The test is whether the student has
*already felt* the thing before it gets a name.

**The technique that makes it land:** when you name it, point back at specific
moments they'll recognise. Not "this lesson is about control" but "every time
you were told to slow down, stop before it got sloppy, keep it small — that was
control." The naming should feel like something clicking into place, not like
new material.

### Base plus add-on

Keep one shared opening routine identical in every lesson — students learn it
once and own it forever. Layer lesson-specific material *after* it, never
swapped in. When a draft arrives with a shortened version, that's shorthand, not
an instruction to replace the real one.

### Never make a student remember a lesson number

Someone repeating a lesson months later, out of order, must still understand it.
Embed the reminder in the cue:

> ✗ "In Lesson 3, you learned to do X."
> ✓ "Until today, you did X. Now..."

> ✗ "Remember the thing from Lesson 5?"
> ✓ "You've done this every time you [concrete familiar experience]."

### One item per card

"Do A *or* B" makes the student choose. Prescribe one; put the alternative in
the progression box.

### Data and prose must agree

If the summary chip says one number and the instruction says another, the
student has to work out which is authoritative — and you've undermined both.
Check every card.

### Full teaching at first introduction; later appearances point back

The first time something appears, give it a whole card. Its second appearance is
three lines and a callback to the feeling they already know.

### Link out to video — don't embed it

Every "watch" link in this project leaves the page. There is not one `<iframe>`
anywhere, and that's a decision rather than an oversight.

**Embedded video turns a learn-course into a watch-course.** If a player sits in
the page, students press play and stop reading. The card is the teaching; the
video is the reference for when words aren't enough.

**Leaving the page hands control back to the viewer.** On a phone a real video
link can open the native app, which gives picture-in-picture — the demo floats
over the lesson while they read and move.

**On ads:** someone will eventually ask for embeds because embeds show fewer
ads. Ad-free viewing is what a platform subscription buys; it isn't your site's
job to work around it.

### How to write a progression

Progression isn't one lever — it's a menu. Pick the smallest change that fits
what actually limits someone on that item:

| Lever | Use when |
|---|---|
| Volume | more work *is* the point |
| Load | technique is already clean |
| Range | the movement has room to grow |
| Leverage | load can move further from the joint |
| Tempo | quality under time |
| Stability | the difficulty can come back |

**Match how you'd actually coach it.** Only default to the gentlest lever when
there's no coaching cue to build from. When it could plausibly go two ways,
ask — don't guess.

---

## Customization recipes

Copy-paste answers to the things people change first. All of these live in the
`<style>` block of each lesson file — and because the tokens and timer are
duplicated on purpose, **a change here means the same change in every file**.

### Change every colour on the site

Edit the tokens in `:root`. Nothing else hard-codes a colour.

```css
:root{
  --paper:#FFF7EA;   /* page background */
  --card:#FFFFFF;    /* card background */
  --line:#EADFCB;    /* borders, dividers */
  --ink:#26140A;     /* body text, borders, headings */
  --ink-2:#7E6A54;   /* secondary text */
  --sun:#FFB300;     /* highlight — timers, "get ready" */
  --aqua:#12A14B;    /* success — "do this" */
  --pink:#D62828;    /* caution — "stop" */
  --lime:#93D500;    /* active — running timer, progress fill */
  --coral:#E8500F;   /* accent — links, buttons */
}
```

For a sober, non-carnival palette, keep the *roles* and change the *values* —
one highlight, one success, one caution, one accent. The layout doesn't care
what the colours are, only that each role stays distinct.

### Stop the flickering / rainbow shimmer

Two separate effects, turned off separately.

**The animated gradient text** (`.shine` on hero words). To stop the movement
but keep the gradient:

```css
.shine{ animation:none; }
```

To remove the gradient entirely and use plain text, delete the `<span
class="shine">` wrapper in the HTML, or:

```css
.shine{
  animation:none;
  background:none;
  -webkit-text-fill-color:var(--ink);
  color:var(--ink);
}
```

**The rainbow divider bars** (`<hr class="stripe"/>`). For a single flat colour:

```css
.stripe{ background:var(--ink); }
```

Or delete the `<hr class="stripe"/>` lines from the HTML.

**Note:** the shimmer already stops automatically for anyone whose device has
"reduce motion" enabled — there's a `@media (prefers-reduced-motion:reduce)`
rule handling it. Keep that rule whatever else you change.

**The timer finish flash** (`.tbtn.done`, four red/gold pulses). To make the
finish static instead:

```css
.tbtn.done{ animation:none; }
```

Think before removing this one — on a phone with the ringer off, the flash may
be the only signal the timer finished.

### Change the fonts

Three font tokens, plus the Google Fonts `<link>` in `<head>`:

```css
--display: /* headings — the loud one */
--body:    /* paragraphs — must be readable at small sizes */
--mono:    /* labels, numbers, chips */
```

Swap the `<link>` for your fonts and update the three tokens. Keep a monospace
for `--mono`: the data chips rely on even character widths to stay aligned.

### Remove the timers entirely

If your subject has nothing to time:

1. Delete the `<div class="timerbank">…</div>` blocks from the HTML.
2. Delete any inline `<button class="tbtn" …>` buttons.
3. Leave the timer JS alone — it does nothing when no `.tbtn` exists.

### Change how many sections a lesson has

There's no fixed number. For each section you add or remove, update: the
`<section class="sec" id="…">`, its `<button data-go="…">` in the nav, the
`.stepno` badges ("Step 3 / 7"), the "Step N of M" text in each `.upnext`
handoff, and `blockMap` at the bottom. **None of these count themselves.**

### Turn off progress tracking

Delete the `<label class="donebox">…</label>` from each card and the
`<div class="progresswrap">` from the nav. The script tolerates their absence.

---

## The hold timers

Every timer button runs the same three-phase countdown:

| Phase | Colour | Meaning |
|---|---|---|
| `Ready 5...1` | yellow | get into position — nothing is timed yet |
| counting | green | the actual work |
| `done` | red + flash | finished — tones, vibration, then resets |

Tap a running timer to cancel. Change the lead-in via `READY_SECS`.

**The yellow phase exists because of testing feedback** — a countdown that
starts before you're ready is worse than no countdown.

**On iPhone, the physical silent switch mutes all web audio.** Nothing can be
done about that from a web page. That's why the finish *also* vibrates (Android)
and flashes the button (everywhere). Don't rely on sound alone.

---

## Layout rules that are easy to break

**1. Never use `overflow-x:hidden` on `html` or `body`.**
It stops sideways scrolling, but it also turns the page into a scroll container,
which **silently breaks every `position:sticky` element inside**. Use
`overflow-x:clip` instead — it clips without creating a scroll container. This
bit this project once: the sticky nav stopped working, nothing errored, and
nobody noticed for a while.

**2. The sticky header chain must stay in order.**
Bars stack at the top, each below the one above:

    .homebar     top: 0
    .jump        top: var(--hbH)
    .timerbank   top: calc(var(--hbH) + var(--jumpH))

Heights are measured in JS (`setJumpH()`) on load, resize, and orientation
change, so they never overlap however the nav wraps. Add another sticky bar to
that chain — don't just give it `top:0`, or it'll land on the nav.

The homebar must be the **first thing inside `<div class="wrap">`**. Further
down it still works, but nobody sees it.

**3. Declare `color-scheme: light`.** Both a `<meta name="color-scheme">` tag
and the CSS property. Without it, Android WebView force-dark, Chrome auto-dark,
and in-app browsers will invert a light design into unreadable near-black.

**4. Diagrams: use SVG, not ASCII art.** Monospace diagrams shatter at phone
width and read as noise to a screen reader. An inline SVG scales, uses your
tokens, and carries `<title>`/`<desc>` for accessibility. **Check text bounding
boxes** — text centred near the viewBox edge gets silently clipped, and you
won't see it until someone tells you a word looks misspelled.

---

## The one rule that keeps this from becoming a mess

**One source of truth per lesson.** A lesson lives in exactly one file. The
index *links* to it; it never embeds a copy. Learned the hard way — there were
once two copies of Lesson 1, and they drifted apart.

---

## Access codes

Each product holds a **list** of codes, not one shared code:

```js
var CODES = {
  product:  ['BATCH26-4K9X', 'BATCH26-7M2P', ...],
  other:    ['...']
};
```

One code per buyer, minted in batches. Codes are compared uppercased and
trimmed, so buyers can type them in lower case or with stray spaces.

### How gating actually works

Three moving parts, in order:

1. **`CODES`** — the lists above. Which strings are valid, grouped by product.
2. **Saved state** — `applyCode()` matches a typed code to a product and sets a
   flag in `localStorage` under `tutl-academy`. The saved object is just
   `{course:true, cookbook:true, bootygarden:true}` — one boolean per product.
3. **`refresh()`** — reads those flags and shows or hides rows. Nothing else
   decides what's visible.

A code never points at a *page*. It sets a flag, and `refresh()` decides what
that flag reveals. That indirection is what lets one code open rows across
different courses, and lets one course answer to two different codes.

### The gate map

This site runs three courses off one `course.html`. Each row falls into one of
three states:

| Tier | What opens it | Where it lives |
|---|---|---|
| **Free** | nothing — always visible | Movement Lesson 1 · Kitchen Manifesto · Booty Garden intro + Lesson 1 |
| **Product** | that product's own code | Movement 2–7 (`course`) · Kitchen list (`cookbook`) · Booty Garden 2–4 (`bootygarden`) |
| **Cross-granted** | a *different* product's code | Booty Garden Lesson 5 — opens with any `course` code |
| **Bundle** | one code sets every flag | all of the above |

Every course keeps a free tier on purpose: someone who never buys anything still
gets a complete, usable lesson, and the paid rows sit visibly beneath it.

### Two gates on one page

The Booty Garden view is the case worth understanding, because one page holds
**two independent gates**:

- Lessons 2–4 need `CODES.bootygarden` — its own small add-on.
- Lesson 5 opens with `CODES.course` — the Academy code, at no extra cost.

In `refresh()` those are two separate checks against two separate flags:

```js
// Lessons 2–4: this product only
if(st.bootygarden){ /* show the list */ }

// Lesson 5: either flag will do
if(st.course || st.bootygarden){ /* unlock the row */ }
```

The `||` is the whole cross-grant. To give an Academy member a lesson from
another course, add their flag to that row's condition — you don't need a new
code, a new product, or a duplicate page.

**Handle the near-miss.** A member who types their Academy code into the Booty
Garden box has entered a *valid* code that doesn't unlock *that* row. Don't let
that read as failure — `applyCode()` returns which product was hit, so say what
it did and didn't do:

```js
if(hit==='bootygarden' || hit==='bundle'){ /* unlocked */ }
else if(hit==='course'){ /* "That's your Academy code — it opens Lesson 5.
                            Lessons 2–4 need a Booty Garden code." */ }
else { /* genuinely wrong code */ }
```

A correct code that silently does nothing is the fastest way to generate a
support email.

### Adding a fourth product

1. Add a key to `CODES` with its list of codes.
2. Add its flag to the `bundle` branch of `applyCode()` — easy to forget, and
   the symptom is a bundle buyer who paid for everything and can't open the new
   thing.
3. Add a `refresh()` branch that shows its rows when the flag is set.
4. Give it a free row so the course isn't a closed door.

`applyCode()` already checks list membership, so none of this is a rewrite.

**Minting:** pick a batch prefix, add four random characters per buyer, avoid
`0`/`O` and `1`/`I` (people transcribe by hand). Keep your own record of which
code went to which buyer **outside this file** — the site can't track that.

**Why per-buyer:** a single shared code is one leak away from public. The
realistic leak isn't someone reading your source — it's one buyer pasting a code
into a group chat. Per-buyer codes mean a leak burns *one line*, which you
delete.

**Why batches retire:** if your product is seasonal or phased, the batch expires
on its own. You get expiry with **no expiry logic, no revocation, and no "my
access stopped working" messages.**

**What this cannot do.** It is **not access control** — codes are readable in
the page source and the unlock lives in the visitor's browser. It's a speed bump
that keeps honest people honest. It also **cannot detect sharing**: a static
site reports nothing, so there's no volume to watch.

**How to spot a leak anyway:** compare things you *can* count — video view
counts, or a free analytics script — against codes sold. On a static site that
gives you no enforcement, but it tells you *when to graduate* to a seller
(Gumroad, Payhip, Podia) that issues real per-sale licence keys.

---

## Where to change common things

| I want to... | Go to |
|---|---|
| Re-skin everything | the `:root` tokens — every lesson file, identically |
| Edit lesson copy | inside `<body>`, marked **SAFE TO EDIT** |
| Add/remove a card | the `<article class="ex">`, then rebuild `blockMap` |
| Add/remove a section | the `<section class="sec">`, its nav button, the step numbers, then `blockMap` |
| Change the timer lead-in | `READY_SECS` in the shared timer JS (every file) |
| Mint/retire codes | `course.html`, search `var CODES` |
| Change what a code unlocks | `refresh()` in `course.html` — the flag checks, not `CODES` |
| Let one course's code open another's lesson | widen that row's check in `refresh()` to accept the other flag too |
| Add a whole new product | `CODES` key → `bundle` branch → `refresh()` branch → a free row |
| Mark a new lesson live | `course.html`: `data-standalone="file.html"` on its row **and** the unlock-bar text |

---

## Before you launch anything built on this

- [ ] **Replace the sample unlock codes.**
- [ ] **Wire up real payment.** Unlocking is client-side localStorage:
      convenient, **not secure**. Fine for a small launch; needs a backend to be real.
- [ ] **Have a lawyer read your disclaimer.** There's one on every page here,
      but it wasn't written by an attorney.
- [ ] **Check on a real iPhone and Mac.** Everything is verified in Chromium at
      360/390/768/1024/1440px, but Safari has its own quirks — `vh` units in
      particular.
- [ ] **Replace placeholder media links** with your own.
- [ ] Once no new lessons are coming, extract `tokens.css` + `timer.js`.

---

## License

**Status: not yet chosen — treat as All Rights Reserved.**

Copyright (c) [YEAR] [YOUR NAME / ENTITY].

Pick one before the first sale. This decision can't be reversed afterwards —
anyone who bought under the old terms keeps them.

| Option | Buyers may | Buyers may not |
|---|---|---|
| **MIT** | use, modify, redistribute, **resell** | — |
| **Commercial, single site** | build one site of their own | redistribute, resell, share files |
| **Commercial, multi-site** | build their own and client sites | redistribute, resell as a template |
| **Custom** | whatever you write | whatever you exclude |

Add a `LICENSE` file at the repo root with the full text of whichever you pick,
and replace the placeholder banner at the top of each template file.

**Separate point worth stating explicitly in your terms:** licensing the
*structure* is not licensing any *course content* built on it. The template is
the product; the curriculum written into it is not.

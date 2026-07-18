# Turn Up the Life — site files

Static HTML. No build step, no framework, no npm. Every file opens directly in
a browser and can be uploaded as-is to the host.

---

## The map

### Course 01 — Movement

| File | What it is |
|---|---|
| `course.html` | **The Academy.** The shell/home for everything. Holds both courses, the curriculum list, and the unlock codes. Contains **no lesson content** — it links out. |
| `trial-class.html` | **Lesson 1 / the free trial.** The marketing front door *and* the course's Lesson 1 — one file serving both, so they can't drift apart. |
| `lesson-2.html` | Lesson 2 — Drive the Hips (dumbbells) |
| `lesson-3.html` | Lesson 3 — Create Tension (bands, travel workout 1) |
| `lesson-6.html` | Lesson 6 — Pack Light, Train Hard (bands, travel workout 2) |

**Not built yet:** Lessons 4 (Increase Density), 5 (Link the Body), 7 (Own Your Body).
Copy `lesson-2.html` as the starting template for 4/5/7 and `lesson-3.html` for
any further band lesson.

### Course 02 — The Kitchen (The Livitarian Way)

| File | What it is |
|---|---|
| `kitchen.html` | Manifesto + Foundations. The **free** tier. Pure content, no JS. |
| `planner-builder.html` | Build-a-day tool. The rules engine (anchors, rotation rule, tallies). |
| `matrix.html` | All 48 recipes in a filterable/sortable table. |
| `planner-recipes.json` | The 41 builder-usable recipes with macros. |

**Not built yet:** the cookbook recipe cards, Current Picks, cooking mode.

### Reference docs (not part of the site)

`cue-library.md` · `exercise-block-spec.md` · `lesson-plan-template.md` · `kitchen-build-notes.md`

---

## The one rule that keeps this from becoming a mess

**One source of truth per lesson.** A lesson lives in exactly one file.
`course.html` *links* to it; it never embeds a copy. This was learned the hard
way — there used to be two Lesson 1s and they drifted apart.

---

## Duplicated-on-purpose blocks (the "don't touch" list)

The four lesson files each carry their own copy of two blocks. They are marked
in the files with a loud `██ DO NOT EDIT HERE — SHARED ██` banner.

1. **The `:root` design tokens** (colors + fonts)
2. **The timer JS** (`beep`, `fmt`, the `.tbtn` countdown)

These are **byte-identical across all four files** — verified by checksum.
If you change one, change all four the same way, or the lessons will drift.

**Why duplicated instead of a shared file?** So each lesson works completely on
its own — you can open, preview, or send any single lesson and it just works,
with nothing else loaded. That matters while Lessons 4/5/7 are still being
built and previewed one at a time.

**The plan:** once Lessons 4, 5, and 7 are built *and tested*, pull these two
blocks into shared files (`tokens.css`, `timer.js`) and link them from every
lesson. The design will be frozen by then, so the isolation stops earning its
keep and the DRY win takes over.

---

## Where to change common things

| I want to… | Go to |
|---|---|
| Change a color anywhere | the `:root` block at the top of the file — every color is a `--name` used everywhere. In lessons, remember it's the shared block (all four). |
| Edit workout copy, cues, reps | inside `<body>`, marked **SAFE TO EDIT** |
| Change the unlock codes | `course.html`, search `var CODES` |
| Add/fix a recipe | `matrix.html` `DATA` array **and** `planner-builder.html` `RECIPES` — keep them in sync |
| Retarget the planner's calories/protein | `planner-builder.html`, the `DAY` object |
| Change what fills each meal slot | `planner-builder.html`, the `SLOTS` array (this is the method — change carefully) |

---

## Before launch — the open TODOs

- [ ] **Replace the three unlock codes** (`ROADREADY`, `LIVITARIAN`, `FULLPLATE`)
- [ ] **Wire up real payment.** Unlocking is currently client-side localStorage:
      convenient, **not secure**. Fine for a small launch, needs a backend to be real.
- [ ] **Add the fitness disclaimer / waiver**
- [ ] **Fill the squat-flow creator's real name + Instagram handle** (currently
      `@creator-handle` placeholder) once permission is confirmed
- [ ] Build Lessons 4, 5, 7
- [ ] Build the cookbook recipe cards + Current Picks + cooking mode
- [ ] **Check on a real iPhone and Mac.** Everything is verified in Chromium at
      360/390/768/1024/1440px, but Safari has its own quirks. Most likely
      suspect if anything looks off: the `min-height:88vh` hero in `kitchen.html`.

---

## Notes on the numbers

Protein and calorie values are **planning estimates** until each recipe is
kitchen-tested. The label on the product you actually bought always wins.
Three recipes store a single value where the source gave a range (High-Protein
Patties 650 kcal, Sorrel/Ginger Tea 40 kcal, Tofu Sauces 5 g / 55 kcal) — those
were midpoint judgment calls and can be adjusted.

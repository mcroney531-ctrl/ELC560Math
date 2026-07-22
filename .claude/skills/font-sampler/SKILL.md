---
name: font-sampler
description: Build a quick, throwaway HTML mockup that renders one or more screens of this app (index.html) once per candidate Google Font, so the user can compare fonts side by side before picking one. Use this whenever the user wants to "try out fonts," "compare fonts," "see how the app looks in different fonts," or says something like "let's make a font sampler" / "font sampler page" / "/font-sampler" — even if they haven't yet said which screens or fonts, since this skill asks for that. This is a one-off comparison tool, not a request to actually change any fonts in index.html itself.
---

# Font Sampler

Generates a single self-contained HTML file that repeats a chosen set of the
app's screens once per candidate font, so the user can scroll through and
compare letterforms/personality before committing. This mirrors picking
Google Fonts and wanting to see them "in context" rather than in isolation.

## Why this shape

The whole point is that a font choice can't be judged from a specimen page —
it has to sit next to the real cards, the real cube, the real Dot bubble,
at the real sizes this app actually uses. So the sampler doesn't invent its
own mockup: it lifts the actual markup and CSS out of `index.html` as it
exists *right now* and just swaps `font-family`. If the app's styling has
changed since the last time this skill ran, the sampler should reflect that
change too — always re-read `index.html` fresh, never reuse a cached copy
of the markup from a previous run or from this file.

## Step 1 — Get the two inputs

If the user's request already contains both of these, skip straight to
Step 2. Otherwise ask (a single question covering both is fine):

1. **Which screen(s)?** Frame the options around the app's actual screens
   so the user can just point rather than describe:
   - Home / Menu Hub
   - Geometry · Unfold the Cube — Step 1 ("Look closely")
   - Geometry · Unfold the Cube — Step 5 ("Your turn" / free explore)
   - Fractions · Build the Pieces — Step 1
   - Algebra · Balance the Scale — Step 1
   - Something else (they describe which step/state)

   Multiple screens are fine and encouraged — the sampler lays them out
   side by side per font. Home + one activity's Step 1 is a good default
   if the user just says "the app" without specifics.

2. **Which fonts?** Names, or a pasted Google Fonts `<link>`/`@import`
   snippet (extract family names from it if given that way).

## Step 2 — Read index.html fresh

Read the current `index.html` in full (or at least its `<style>` block and
the markup for the requested screens/steps). Don't rely on memory of past
conversations about this file — colors, class names, and the Dot character's
markup have all changed before and will likely change again.

## Step 3 — Build the sampler HTML

A single file, self-contained except for the live Google Fonts request:

- **Head**: `preconnect` to `fonts.googleapis.com` and `fonts.gstatic.com`,
  then one combined `css2` stylesheet link covering every requested family
  in a single request (URL-encode spaces as `+`, e.g. `Bubblegum+Sans`).
  Before finalizing, sanity-check the family names resolve — a quick
  `curl` of the constructed URL with a browser-like `User-Agent` and
  grepping the response for `font-family: '...'` confirms every name was
  spelled/encoded right and Google actually serves it. A 200 status alone
  isn't enough proof; Google's API can return 200 with an empty or partial
  stylesheet if a family name doesn't match anything.
- **CSS**: copy over only the rules the requested screens actually use —
  `:root` custom properties, the base reset, and the classes touched by
  each selected screen (e.g. Geometry steps pull in `.stage`/`.face`/
  `.hinge`/`.dot-*`; the Fractions/Algebra stages pull in their own
  `.fbar`/`.astage`/etc. instead). No need to drag in CSS for screens
  nobody asked to see. Check for any element that sets an explicit
  non-inherited `font-family` (the buttons in this app deliberately use
  `font-family: inherit` so they pick up the sampler's override — if a
  future version of the app adds a button that doesn't, add a
  `.specimen button { font-family: inherit; }` safeguard so it doesn't
  silently stay on the browser's default font).
- **Markup**: for each requested screen, reproduce it statically — no JS
  interactivity needed, this is a font comparison, not a working demo.
  Hardcode a sensible default/step-1-like state: counters showing "?",
  step-dots with the right step marked `.on`, the guide's step-label and
  Dot bubble text filled in from that step's copy, choice buttons present
  but not answered, the primary action button visible (disabled if that's
  its natural resting state). For the Geometry cube specifically, replace
  the JS-driven `orbit`/`centerer`/hinge transforms with fixed inline
  ones — `rotateX(-22deg) rotateY(-34deg)` on `.orbit` and the fold-progress-1
  (fully folded) values for the hinges and centerer reproduce the app's
  default resting view without needing any script.
- **Repeat per font**: use a `<template>` holding one specimen's markup
  (all requested screens together) plus a small JS loop over a `fonts`
  array that clones the template once per font, sets a `--sampler-font`
  custom property on each clone's root (`font-family: var(--sampler-font)`
  cascades down to everything via inheritance), and labels it with the
  font name and its position (e.g. "3 / 20"). Don't hand-duplicate the
  markup once per font — that's what the loop is for, and it's the only
  way to guarantee 20 near-identical blocks actually stay identical.
- **Chrome**: a small sticky header at the top identifying the page as a
  font sampler, with one line reminding the reader it needs a real browser
  with internet access (the fonts load live).

## Step 4 — Verify before sending

If a headless browser is available, load the file and check for console
errors and that the expected number of specimen sections rendered. Sandboxed
tool environments sometimes can't reach external font hosts even when the
user's own browser will (proxy/network quirks specific to the tool sandbox,
not the deliverable) — if the browser check fails only on the *external
font request* while everything else (structure, specimen count, no JS
errors) checks out, that's fine to ship; don't burn time fighting the
sandbox's network path. The `curl` check from Step 3 is the real signal
that the fonts themselves are valid.

## Step 5 — Deliver

Send the file with `SendUserFile`, not as a hosted Artifact — Artifacts run
under a strict CSP that blocks the exact external font requests this file
depends on, so it would silently fall back to a system font and defeat the
whole point. Mention in the caption that it needs to be opened in a real
browser with internet access.

Treat the output file as disposable scratch output, not something to commit
to the repo — this is a comparison tool for the user's own eyes, not a
project asset.

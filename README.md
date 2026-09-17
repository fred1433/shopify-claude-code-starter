# One Shopify change, decided before it was written

[![theme check](https://github.com/fred1433/shopify-claude-code-starter/actions/workflows/theme-check.yml/badge.svg)](https://github.com/fred1433/shopify-claude-code-starter/actions/workflows/theme-check.yml)

A shop owner asks for a small thing on the product page. This repository is the
whole answer, in the order it happened: what the theme already did, what actually
needed code, the code, the mistake the check refused, and how to do the next one
without me.

**This example uses Dawn. Your theme and store have not been accessed. The
workflow would be adapted to your chosen theme.**

## The exercise

Read it in [`docs/exercise.md`](docs/exercise.md). Four steps, about five minutes,
nothing to install:

1. **The request**, said the way an owner says it.
2. **The decision, before the code.** Most of it needed none: Dawn's text block
   and an alternate product template do it from the theme editor, and that is
   written down rather than quietly skipped. Code buys exactly one thing here,
   and the file it costs is named.
3. **The change, and what the check refused.** A real refusal, with the command,
   the rule, the exit code and the proof that nothing was committed, in
   [`docs/receipts/`](docs/receipts/2026-09-17-refused-commit.md). The mistake was
   introduced on purpose.
4. **Doing it again, yourself.** A variation to try, what to check before it goes
   live, and how to undo any of it.

The two verdicts this repository can honestly give:

- **Automated checks passed.**
- **Shopify rendering and editor behaviour: not tested.**

Theme Check is a static analyser: it reads Liquid and JSON. It does not render a
page, open the theme editor or talk to a shop. Seeing the block sit right under
the buy button is a preview on a development theme, which is a different verdict.

## Reproducing it in VS Code

What you need first: **Node.js 22.12 or newer**, VS Code, and git. For the
preview step only, a Shopify store where you can create a development theme.
Steps 1 to 3 below need none of that.

```bash
git clone https://github.com/fred1433/shopify-claude-code-starter.git
cd shopify-claude-code-starter
npm install        # installs the Shopify CLI, wires the commit hook
npm run check      # the whole theme, about four seconds, zero errors
```

Then open the folder in VS Code and start Claude Code in it. It reads
[`CLAUDE.md`](CLAUDE.md) on its own. The opening move on any request is the one
used here:

> Inspect the existing theme first. Explain whether this can be done in the theme
> editor, which files would need changing, and why. Do not edit anything yet.

To preview on a real shop, `npm run dev` asks for a store and creates a
development theme nobody else can see. Nothing here touches a published theme.

## What is in here

| | |
| --- | --- |
| `CLAUDE.md` | Instructions for Claude, backed by automated checks and human review. Where files go, what is never edited, and which rule each check enforces. |
| `.theme-check.yml` | What refuses a commit. One severity raised on purpose, with the reason next to it. |
| `.githooks/pre-commit` | Runs the check before a commit is recorded. Wired by `npm install`. |
| `.github/workflows/` | The same check again on every push, where nobody can skip it. |
| `.vscode/` | The Liquid extension, format on save, two tasks. |
| `docs/` | The exercise, and the dated receipts. |
| everything else | Dawn 16.0.0, unchanged apart from the lines listed below. |

## Dawn, and what was changed in it

Base: **Dawn 16.0.0**, Shopify's reference theme, released 10 August 2026, commit
`bc39a7d2024f1e5c14c42f855bd3552b4913e204`.

`LICENSE.md` is Shopify's own, kept word for word. It is not a plain open source
licence: it allows this code to be used and modified **to build Shopify themes**,
and requires its notices to be kept.

Dawn already ships a Theme Check configuration and GitHub Actions of its own.
They are replaced here by a smaller pair, the same checker run at commit time and
on push. The tooling is not the point; the decisions written down next to it are.

One Dawn file is edited, and here it is, so that a future Dawn update knows where
to look:

| File | Change |
| --- | --- |
| `sections/main-product.liquid` | 25 added lines in three places, each marked `shopify-claude-code-starter`. Nothing removed. |

## What this is not

It is not a shop, a theme for sale, or a training course. It is one change, done
properly, with the reasoning kept. The wording it ships is **sample text** and
describes nothing real.

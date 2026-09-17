# CLAUDE.md

**Instructions for Claude, backed by automated checks and human review.**

Claude Code reads this file at the start of every session opened in this folder.
Instructions alone are not a guarantee: a model can ignore them, and this one has
no way of seeing the shop. So every instruction below that a tool can verify says
which tool verifies it, and the ones no tool can verify are the ones a human
reads the diff for.

Two readers: Claude, and the person driving it.

---

## The theme

- Base: **Dawn 16.0.0**, Shopify's reference theme, released 10 August 2026,
  commit `bc39a7d2024f1e5c14c42f855bd3552b4913e204`, copied unchanged.
- `LICENSE.md` is Shopify's own licence, kept word for word. It is not a plain
  open source licence: it allows this code to be used and modified **to build
  Shopify themes**, and requires its notices to be kept. Read it before reusing
  any of it elsewhere.
- Dawn already ships a theme check configuration and GitHub Actions of its own.
  What this repository adds is not tooling: it is the exercise, the decision
  taken before the code, and the check wired into the moment a commit is made.

## The rule that explains every other rule

**The shop owner changes wording, figures, colours and order from the theme
editor. Not from the code.** If a change to the shop needs VS Code, either the
block was built wrong, or the change genuinely needs code, and that is a decision
someone takes on purpose, not by accident.

## Before writing any code

The first question is never "how do I build this". It is **"does this need code
at all"**. Dawn does a great deal from the theme editor: blocks that can be added,
reordered and worded per template, alternate templates for products that differ,
a Custom Liquid block for the impatient.

So the opening move on any request is:

> Inspect the existing theme first. Explain whether this can be done in the theme
> editor, which files would need changing, and why. Do not edit anything yet.

Answer that in writing, in `docs/`, before touching a file. `docs/exercise.md` is
what one of those answers looks like.

## Where things go

| Folder | What belongs there |
| --- | --- |
| `sections/` | One file per block of a page. Its `{% schema %}` is exactly what the owner sees in the theme editor. |
| `snippets/` | Reusable markup. The body of any loop, and any block big enough to have its own name, belongs here, rendered with `{% render %}`. |
| `assets/` | CSS, JS and images shipped with the theme. One stylesheet per component, named `component-<name>.css`. |
| `templates/` | JSON files saying which sections appear on a page type. An alternate template is how some products get something others do not, with no code at all. |
| `locales/` | 51 translation files. Read rule 7 before touching one. |
| `config/` | Theme-wide settings. `settings_data.json` is written by the theme editor: treat it as the owner's file, not ours. |
| `layout/` | The page frame. A change here reaches every page of the shop. |

## Rules

1. **Everything the owner may want to change is a setting or a block.** No
   wording, no figure, no link, no colour written into Liquid. No tool enforces
   this one. It is the reason all the others exist.

2. **A setting read in Liquid must be declared in the schema.** A setting read
   but never declared does not fail loudly: it renders empty, and the field never
   appears in the theme editor, so the owner can never fix it from the admin.
   Read the schema before reading a setting.

3. **Every file referenced must exist in `assets/`.** Checked: `MissingAsset`,
   severity error. A stylesheet renamed but still referenced is the ordinary way
   a change reaches real customers unstyled. It is the mistake this repository
   demonstrates, on purpose, in `docs/receipts/`.

4. **Never load a script, a stylesheet or a font from another company's server.**
   Checked: `RemoteAsset`, raised from warning to **error** in `.theme-check.yml`.
   That severity is this project's decision, not a Shopify default: a third party
   server adds a connection to every page view and can change or vanish without
   telling anyone.

5. **Scripts are deferred.** A plain `<script src>` stops the browser drawing the
   page until it has downloaded. Checked: `ParserBlockingScript`, error.

6. **Images carry `width` and `height`.** Without them the page jumps while it
   loads. Checked: `ImgWidthAndHeight`, error.

7. **New labels in a schema are plain English strings, not `t:` keys.** Measured
   on this theme on 17 September 2026: a `t:` key that exists nowhere gives 2
   errors (`ValidSchemaTranslations`); the same key added to
   `locales/en.default.schema.json` alone gives **38** (`MatchingTranslations`,
   one for every other locale file that now lacks it). Two ways to stay green:
   write the label as plain text, or reuse a key that already exists in every
   locale file (`t:sections.all.colors.label`, `t:sections.all.padding.*`).

8. **The body of a loop goes in a snippet**, rendered with `{% render %}`.
   `{% include %}` is dead, never write it.

9. **One component, one stylesheet**, loaded with
   `{{ 'component-name.css' | asset_url | stylesheet_tag }}`. The only inline
   `{% style %}` allowed is the per-section padding block Dawn already uses.

10. **Editing one of Dawn's own files is a decision, never a reflex.** It has a
    price: every Dawn update afterwards has to be re-applied around it. When
    there is no other way, as for a new block inside the product information
    section: make the smallest possible change, mark it with a comment naming
    this project, and add the file to the list in `README.md`. When there is
    another way, take the other way. Never touch `config/settings_data.json` or
    `locales/`.

11. **No new JavaScript library.** Dawn already ships what a storefront needs.

12. **Never paste Liquid into the Custom Liquid block in the admin.** It works
    today, and it is invisible to git, never checked, and gone the day the theme
    is replaced.

13. **When the request is ambiguous, stop and ask.** Propose the smallest change
    that answers it, say what it will touch, wait. Do not build three options.

## What the check can and cannot tell you

`npm run check` runs Shopify's Theme Check: a **static analyser of Liquid and
JSON**. It reads the files. It does not render a page, does not open the theme
editor, and does not talk to a shop.

So a green check means: no error found in the code. It does not mean the block
looks right, or behaves right in the editor. That part is a human opening a
preview on a real store. Both are needed, and they are not the same statement.

Green means **zero errors**. Warnings are not zero and that is normal: Dawn
itself ships 9 of them. The rule here is zero errors, and no new warning in a
file we wrote.

Never use `git commit --no-verify`.

## Commands

```bash
npm install          # installs the Shopify CLI and wires the commit hook
npm run check        # theme check, whole theme, about four seconds
npm run dev          # live preview, needs a store and a development theme
npm run setup        # re-wires the commit hook if it ever stops running
```

## Branches

- `main` is what is ready for the live theme.
- `preview/<what-it-is>` is work in progress. Push it to an **unpublished** theme
  (`shopify theme push --unpublished`), look at it in a browser, then merge.
- The live theme is never edited in the Shopify admin code editor. Changes made
  there are invisible to git and get overwritten without warning.

## The worked example

`docs/exercise.md` holds one request, start to finish: what was asked, what the
theme already did, what needed code and why, what the check refused, and how to
undo it. The code it produced is one block in the product information section:
`snippets/product-note.liquid` and `assets/component-product-note.css`, both new,
plus 25 added lines in three marked places inside `sections/main-product.liquid`,
Dawn's own file, with nothing removed from it.

Its wording is **sample text**. It describes nothing real and is there to be
replaced.

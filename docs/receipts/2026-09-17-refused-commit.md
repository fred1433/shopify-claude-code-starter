# A commit the check refused

**17 September 2026, 12:59 UTC.**

**This mistake was introduced on purpose**, to exercise the check and to show
what a refusal looks like. It is not a reconstruction of something a model did:
it is a real fault, put in a real file, put through the real tool.

The fault chosen is the ordinary one. It was picked after checking what the tool
actually detects, not from a list of things that sound plausible.

## What was wrong

`sections/main-product.liquid` gained this line:

```liquid
{{ 'component-product-note.css' | asset_url | stylesheet_tag }}
```

and the file it names, `assets/component-product-note.css`, did not exist. In a
live shop, nothing would have crashed. The block would simply have reached
customers with no styling at all, on every product page, until someone noticed.

## What ran

| | |
| --- | --- |
| Command | `git commit -m "product note block under the buy button"` |
| What the hook runs | `shopify theme check --fail-level error --path .` |
| Tool | Shopify CLI 4.8.0, Theme Check 3.29.0 |
| Configuration | `.theme-check.yml`, `extends: theme-check:recommended` |
| Rule | `MissingAsset`, severity **error** (a Shopify default, not a local setting) |
| Threshold | errors refuse the commit, warnings do not |
| Result | 158 files inspected, **1 error**, 9 warnings |
| Exit code | **1** |
| Commit | **not created**, the change stayed in the staging area |

## What it printed

```
sections/main-product.liquid

[error]: MissingAsset
'assets/component-product-note.css' does not exist

19  {{ 'component-product-note.css' | asset_url | stylesheet_tag }}
```

```
Theme Check Summary.
158 files inspected with 10 total offenses found across 8 files.
1 errors.
9 warnings.
```

```
------------------------------------------------------------------
Commit refused. Nothing was committed.

The theme check found errors, listed above. Each one names the rule
it broke; the rules are explained in CLAUDE.md.

Fix them, then commit again. The check takes about four seconds.
------------------------------------------------------------------
```

The full transcript, warnings included, is in
[`2026-09-17-refused-commit.txt`](2026-09-17-refused-commit.txt).

## The 9 warnings

They come from Dawn itself, not from this change: unused variables, one long
snippet, a snippet no other file uses. They are printed every time and they never
refuse anything. Only errors do. Silencing them would have been easy and would
have hidden the next real one.

## The fix

Writing the missing file, `assets/component-product-note.css`. The same command
then passed with zero errors and the same 9 warnings, and the commit was created.

## What this does not prove

Theme Check reads Liquid, JSON and the files around them. It does not render a
page, does not open the theme editor, and does not talk to a shop. It caught a
file that was not there. It cannot tell anyone whether the note looks right on a
product page. That second verdict needs a preview on a real store, and it is a
separate statement:

- Automated checks passed.
- Shopify rendering and editor behaviour: not tested.

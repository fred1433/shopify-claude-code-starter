# The same thing again, from a clean clone

**17 September 2026, 13:46 UTC.** Fifteen seconds, start to finish.

Everything on the page and in the README says what happens when someone clones
this repository and runs the three commands. This is that, done on a machine
where the repository had never been, reading nothing but what GitHub serves.

## What was run, and what came back

| | |
| --- | --- |
| `git clone` then `npm install` | Node 26.8.2, install finished, no manual step |
| `git config --get core.hooksPath` | **`.githooks`** (wired by `npm install`, not by hand) |
| `npm run check` on the clone as published | 158 files, 9 offenses, **0 errors**, **exit 0** |
| The same fault, put back: the stylesheet the section names, removed | `git commit` printed `MissingAsset`, then **Commit refused. Nothing was committed.** |
| `git log --oneline -1` right after the refusal | `4fddd32`, the commit the clone came with. **HEAD unchanged.** |
| The file restored, `npm run check` again | 158 files, 9 offenses, **0 errors**, **exit 0** |

The 9 offenses in the green runs are Dawn's own warnings. They are printed every
time and they refuse nothing. Only errors do.

The full transcript is in
[`2026-09-17-clean-clone-replay.txt`](2026-09-17-clean-clone-replay.txt).

## What it proves, and what it does not

It proves the recipe works on a machine that has never seen this project: the
hook installs itself, the theme is clean as published, and the check really does
stop a commit rather than warn about it afterwards.

It proves nothing about how the block looks on a product page. That verdict needs
a preview on a development theme, and it is kept separate on purpose:

- Automated checks passed.
- Shopify rendering and editor behaviour: not tested.

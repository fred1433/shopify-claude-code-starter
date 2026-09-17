# One change, start to finish

17 September 2026.

Steps 1 and 2 were written before any file was edited. That order is the point of
the whole exercise: the decision comes before the code, and it is written down so
it can be argued with.

This example runs on Dawn. Your theme and store have not been accessed. The
workflow would be adapted to your chosen theme.

---

## 1. The request

Said the way a shop owner says it, not the way a developer would write a ticket:

> On the product page, under the Add to cart button, I want a short line of
> reassurance. One sentence, and I want to be able to change the words myself.
> It should only appear on the products it applies to, not on all of them.

## 2. The decision, before the code

The first thing asked of Claude, word for word:

> Inspect the existing theme first. Explain whether this can be done in the theme
> editor, which files would need changing, and why. Do not edit anything yet.

The answer below was then checked by hand against the theme, because a model's
account of a codebase is a claim, not a fact.

### What the theme already does, with no code at all

- The product information column is a **list of blocks** you rearrange in the
  theme editor. Dawn ships a **Text** block: write the sentence, drag it under
  the buy buttons, done. The words stay editable by the owner forever.
- Dawn also ships an **Icon with text** block, if the line needs a small picture
  next to it.
- Showing it on some products and not others has an answer too, and it is not
  code: an **alternate product template**. Duplicate the product template in the
  theme editor, put the block in one copy only, then assign that template to the
  products concerned from the product page of the admin.

So for a fixed list of products chosen by hand, this request needs **no code**,
and saying so is worth more than building something.

### Where that stops, and code starts

It stops when the rule has to be **automatic**: every product carrying the tag
`fragile`, including the ones added next year by someone who has never heard of
this conversation. A theme editor block cannot ask a question about the product
it happens to sit on. That single sentence is what the code below buys, and
nothing more.

### What was possible and was refused

Dawn ships a **Custom Liquid** block: you can paste code into it from the admin
and it runs immediately. It would have answered this request today, with no
repository and no tools.

It is refused here, and this is the reason: code pasted in the admin is invisible
to git, no check ever reads it, nobody can see who changed it or when, and it
disappears the day the theme is replaced. It buys an afternoon and costs the
next year.

### What the code touches, and why

| File | What happens to it |
| --- | --- |
| `sections/main-product.liquid` | **Dawn's own file, edited.** 25 added lines in three marked places, nothing removed. A block cannot be declared to a section from outside that section's file, so there is no way around it. |
| `snippets/product-note.liquid` | New, ours. The markup and the condition on the tag. |
| `assets/component-product-note.css` | New, ours. The few lines of styling. |

Editing one of Dawn's files has a price, and it is paid later: every future Dawn
update has to be re-applied around those 25 lines. That is why they are marked
with a comment naming this project, why nothing is removed, and why the file is
listed in the README. It is a decision taken with the cost in view, not a reflex.

The block that comes out of it has two settings, both in the theme editor:
the wording, and the tag it reacts to. Leave the tag empty and it shows
everywhere. No developer needed to change either.

## 3. The change, and what the check refused

The code is 54 added lines: 25 in Dawn's product section, 29 in a new snippet,
plus a small stylesheet.

The first attempt at committing it was refused. The stylesheet was named in the
section but had not been written yet, so the block would have reached customers
unstyled on every product page. The check found it, the commit was not created,
and the terminal said so in one line.

The whole thing is in
[`receipts/2026-09-17-refused-commit.md`](receipts/2026-09-17-refused-commit.md):
the faulty line, the command, the rule, the exit code, and the proof that nothing
was committed. **That mistake was introduced on purpose**, to exercise the check.

Then the missing file was written, the same command passed, and the commit was
created. Two lines of terminal, four seconds, no meeting.

What this earns, and what it does not:

- Automated checks passed.
- Shopify rendering and editor behaviour: not tested.

The second line is not a caveat added out of modesty. Theme Check reads files; it
does not open a browser. Seeing the note sit correctly under the buy button is a
preview on a development theme, and that is a different verdict from a different
tool.

## 4. Doing it again, yourself

The point of the exercise is that the next one does not need me.

**A variation to try.** Ask for a second note, shown only on products tagged
`clearance`, in a different wording. Nothing in the code needs to change: add the
block a second time in the theme editor, type the tag, type the words. If that
works without a developer, the block was built right. If it does not, that is the
bug, not the wording.

**What to check before it goes live.**

```bash
npm run check          # zero errors, four seconds
shopify theme push --unpublished    # a copy nobody can see but you
```

Then look at the preview: a product with the tag, a product without it, and a
phone width. The check cannot do any of those three.

**How to go back.** Every step above is a commit, so nothing is a one way door:

```bash
git log --oneline               # find the change
git revert <commit>             # undo it, keeping the history honest
git checkout -- .               # throw away edits not yet committed
```

And in the shop itself, the theme you published last is still there, untouched,
for as long as you keep it.

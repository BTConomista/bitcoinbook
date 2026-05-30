# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is the complete source of the book **_Mastering Bitcoin: Programming the Open Blockchain_ (3rd edition)** by Andreas M. Antonopoulos and David A. Harding, published by O'Reilly Media. It is **prose, not a software project**: the deliverable is a book rendered to PDF/EPUB/MOBI/HTML, written in [AsciiDoc](https://asciidoc.org/).

This particular fork is an **Italian translation** of the upstream English text. The chapter `.adoc` files contain translated Italian prose, while structure, anchors, image references, code listings, and admonition markup are kept identical to upstream English so the book still builds and cross-references resolve. When editing chapter content, translate/revise prose but **do not change anchor IDs, image filenames, cross-reference targets, or code**.

## Repository layout

- **`ch01_*.adoc` … `ch14_*.adoc`** — the fourteen chapters (the bulk of the content).
- **`appa_whitepaper.adoc`, `appb_errata.adoc`, `appc_bips.adoc`** — appendices.
- **`preface.adoc`, `glossary.asciidoc`** — front/back matter.
- **`book.adoc`** — master file that `include::`s every chapter/appendix in order. This is what the build compiles. **Any new chapter file must be added here** (and to `BOOK.md` / `atlas.json`).
- **`BOOK.md`** — the GitHub-readable table of contents (one Markdown link per chapter). Keep in sync with `book.adoc`.
- **`atlas.json`** — O'Reilly Atlas build config: the ordered file list and the output formats (pdf/epub/mobi/html). Mirrors `book.adoc`'s include order.
- **`images/`** — figures, named `mbc3_NNNN.png` (chapter-figure) or `mbc3_aaNNN.png`. `draft_images/` holds work-in-progress art.
- **`code/`** — runnable example programs referenced by the text (e.g. `rpc_example.py`, `max_money.py`). Python examples use `python-bitcoinlib` against a local Bitcoin Core node.
- **`snippets/`** — captured command/RPC output embedded into chapters.
- **`contrib/`** — externally-sourced appendix material.
- **`meta/`** — `github_contrib.adoc` (contributor attribution list) and per-edition change logs.
- **`*.html`** (cover, copyright, titlepage, colophon, etc.) — Atlas-rendered book furniture, not chapter content.
- **`tools/`** — validation/reporting scripts (see below).
- `theme/` — O'Reilly SCSS theme; not edited as part of authoring.

## Building and validating

There is **no Makefile or CI workflow**; validation is done with the `tools/check` script, which is the canonical "does it build" check:

```bash
asciidoctor --failure-level=WARN -v book.adoc -o book.html   # render the whole book
htmlproofer --disable-external book.html                      # validate links/anchors/images
```

`tools/check` runs both. These tools (`asciidoctor`, `htmlproofer` from the html-proofer Ruby gem) are **not pre-installed** in this environment — install them before running, or reason about AsciiDoc validity manually. A broken cross-reference (`<<id>>` with no matching `[[id]]`) or a missing image will fail `htmlproofer`.

Run the terminology linter before finalizing prose changes:

```bash
tools/discouraged.sh    # flags discouraged words/spellings across .adoc/.asciidoc files
```

## AsciiDoc conventions used in this book

- **Anchors / cross-references:** block anchors are `[[anchor_id]]`; references are `<<anchor_id>>`. Chapter top-level anchors are tracked in `tools/chapter_ids.tsv` — these IDs are referenced from other chapters and must not change.
- **Admonitions:** `[TIP]`, `[WARNING]`, `[NOTE]` blocks delimited by `====`. Sidebars use `****` with a `.Title` line above.
- **Figures:** `[[anchor]]` + `.Caption` + `image::images/mbc3_NNNN.png["alt text"]`.
- **Math:** `[latexmath]` blocks delimited by `++++`.
- **Inline literals:** monospace via `+...+` or backticks (e.g. `+secp256k1+`).
- **Index entries:** `(((term, subterm)))` / `indexterm`.

## Contribution rules (from CONTRIBUTING.md / README.md)

- **One pull request per `.adoc` file** to keep merges small; use a separate commit per logical change.
- Contributions target the **`develop`** branch upstream.
- **Unix LF line endings only.** Do not let an editor rewrite line endings — it makes diffs show the whole file as changed.
- **Don't submit pure typo/grammar/syntax fixes** for the English text — those are the copy editor's job. Submit substantive corrections an expert reviewer would catch. (This rule is about upstream English; translation revision is the active work in this fork.)
- Externally-sourced text must carry a compatible open license and a source/license/added-by comment block:
  ```asciidoc
  ////
  Source: https://example.com/originaltext
  License: CC0
  Added by: @username
  ////
  ```
- Attribution for a contribution goes in `meta/github_contrib.adoc`.
- **Licensing/PDF caveat:** the 3rd edition is CC-BY-NC-ND. Do not generate or commit PDF/EPUB renders of the current edition — distributing derivatives violates the license. Build artifacts (`book.html`, `_build/`, `dist/`, `*.pdf`) are git-ignored.

## Discouraged terminology

`tools/discouraged.sh` enforces editorial spelling/terminology. Avoid these in prose (they are acceptable only inside quoted code/JSON/RPC names, which the script skips). Notable rules:

- Write BIP references as `BIP70`-style with no hyphen/space and no `BIP0` (the script flags `BIP-1`, `BIP 1`, `BIP0`).
- Don't use the bare protocol field names as prose words: `nVersion`, `nSequence`, `nLocktime`, `locktime`, `vin`, `vout`, `scriptSig`, `scriptPubKey`, `redeemScript`, `nAmount`, `nValue`.
- Avoid: `off-chain`, `on-chain`, `feerate`, `m-of-n`/`n-of-n`/`k-of-n`/`m-of-m`, `fingerprint`, `block chain` (use "blockchain"), `bitcoin's`, `witness field`, `witness element`, `blockchain.info`, `blockchain.com`.
- Avoid the `word---word` (triple-dash between letters) construction.

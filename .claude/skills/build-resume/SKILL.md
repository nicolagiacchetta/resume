---
name: build-resume
description: Build resume.pdf from the LaTeX sources in this repo using XeLaTeX. Use when the user asks to rebuild/regenerate/compile the resume, or after editing cv_nicola.tex, myInfo.tex, resume.tex, or formatCV.cls.
---

# Build resume.pdf

The resume uses `fontspec` with custom fonts under `fonts/`, so it **must** be built with `xelatex` (not `pdflatex`). Entry point: `resume.tex`.

## Build command

Run from the repo root (so the relative `fonts/` path resolves):

```
eval "$(/usr/libexec/path_helper)"
xelatex -interaction=nonstopmode resume.tex
xelatex -interaction=nonstopmode resume.tex
```

The first `eval` line ensures `/Library/TeX/texbin` is on `PATH` for this shell (BasicTeX installs there but doesn't persist on PATH across every shell session). Run `xelatex` twice so cross-references and bookmarks settle; the first pass emits a "Rerun to get cross-references right" warning that the second pass clears.

Expected output: `resume.pdf` (1 page). Ignore these benign warnings that appear on every build:
- `Underfull \hbox (badness 10000)` — template spacing, pre-existing.
- `! Extra }, or forgotten \endgroup.` near `l.220` of `formatCV.cls` — template quirk; PDF still produces correctly.

If the build actually fails (no PDF written, or `! Emergency stop`), read `resume.log` to find the missing package, then `sudo tlmgr install <pkg>`.

## Environment setup (first-time only)

If `xelatex` is not found, install BasicTeX plus the packages this resume needs:

```
brew install --cask basictex
eval "$(/usr/libexec/path_helper)"
sudo tlmgr option repository ctan
sudo tlmgr update --self
sudo tlmgr install fontspec enumitem xcolor collection-fontsrecommended fontawesome textpos
sudo tlmgr update --all
```

Notes:
- The `brew install --cask basictex` step needs the user's sudo password interactively — if running from an agent without a TTY, ask the user to run it in their own shell.
- `tlmgr option repository ctan` switches to the auto-selecting CTAN mirror; the default Cambridge mirror has been unreliable.
- `tlmgr update --all` is required because a fresh BasicTeX 2026 ships with a `unicode-math` / `fontspec` version mismatch that triggers `Unknown option 'vargreek-shape'`; updating resolves it.
- Full MacTeX (`brew install --cask mactex-no-gui`, ~5 GB) is an alternative that comes with every package preinstalled, no `tlmgr install` needed.

## Files

- `resume.tex` — entry point; `\input`s the others.
- `cv_nicola.tex` — resume content (experience, education, skills).
- `myInfo.tex` — name, contact info, photo path.
- `formatCV.cls` — layout/styling class.
- `fonts/` — Lato, Raleway, FontAwesome glyphs loaded via `fontspec` (paths are relative, so always build from repo root).

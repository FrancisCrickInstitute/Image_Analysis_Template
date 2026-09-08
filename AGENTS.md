# AGENTS.md

## What This Repository Is

This is `CALM_Template`, a **GitHub template repository** maintained by the Francis Crick Institute. Its purpose is to give researchers a starting point for publishing reusable image-analysis code (ImageJ/FIJI macros, CellProfiler pipelines, ilastik projects, Jupyter notebooks, Python/MATLAB scripts, etc.).

It is **not** a conventional application. There is no build system, test suite, package, framework, CI pipeline, or source code beyond a single demo notebook. Agents should treat it as documentation + a working example, not as software to extend with feature logic.

The only functional artifact is `segment_image.ipynb`: it reads a grayscale TIF, applies Otsu thresholding, displays the original vs. binary image, and saves the binary result as a PNG.

## Project Structure

```
.
├── README.md                 # Primary documentation; describes purpose and setup steps
├── pixi.toml                 # Pixi workspace: env, dependencies, tasks (Python 3.14)
├── pixi.lock                 # Lockfile (auto-generated; do not edit by hand)
├── requirements.txt          # conda/pip install path (matplotlib, scikit-image, jupyter)
├── segment_image.ipynb       # The single demo notebook (the "main" code)
├── resources/                # Embedded screenshots used by README
│   ├── New_Repo.png
│   └── Select_Template_Screenshot.png
├── test_data/
│   ├── input/test_input.tif  # Sample grayscale input image
│   └── output/test_output.png# Expected output (thresholded PNG)
├── .github/workflows/trello.yml  # Optional Trello integration (GitHub Action)
├── LICENSE
└── AGENTS.md                 # This file
```

## Environment and Dependencies

The project now manages its environment with [Pixi](https://pixi.sh) rather than plain conda/pip. `pixi.toml` is the source of truth:

- **Python `>=3.14.7,<3.15`** via the `conda-forge` channel.
- Conda deps: `pixi-pycharm` (pulling in a bundled PyCharm for editing the notebook).
- PyPI deps: `matplotlib`, `scikit-image`, `jupyter`.
- `pixi.lock` is a generated lockfile (marked as generated/binary in `.gitattributes`); never hand-edit it.
- `pixi.toml` declares `platforms = ["win-64"]` — the environment is currently Windows-only in practice.

- `requirements.txt` is the conda/pip install path and lists `matplotlib`, `scikit-image`, and `jupyter`.

`requirements.txt` exists as the conda/pip install path and lists `matplotlib`, `scikit-image`, and `jupyter`. Both environment toolchains are supported side by side — Pixi (`pixi.toml`) and conda + `requirements.txt` — so do not remove either one. The README should document both options.

Do not add runtime dependencies casually; the template is meant to stay lightweight. If a dependency is genuinely needed, add it to the correct section of `pixi.toml` (`[dependencies]` for conda, `[pypi-dependencies]` for pip) and regenerate `pixi.lock`.

## Key Commands

There are no build, test, or lint commands. The workflow is interactive Jupyter, run inside the Pixi environment:

```bash
# Install dependencies into the Pixi environment (first time / after pixi.toml changes)
pixi install

# Launch the notebook (must be run from the repo root; see paths note below)
pixi run jupyter notebook segment_image.ipynb

# Run any task defined in the [tasks] table of pixi.toml (none defined yet)
pixi run <task>
```

The README's conda instructions (`conda create --name calm_template ...`) are stale and predate the Pixi migration. The notebook is also runnable via Binder (link in the README badge).

## Conventions and Gotchas

- **Environment is Python 3.14** (Pixi, `conda-forge`), matching the README badge and `requirements.txt` path.
- **Two supported environment toolchains coexist**: Pixi (`pixi.toml` + `pixi.lock`) and conda + `requirements.txt`. Both target **Python 3.14** (see the badge); do not remove either one.
- **Notebook paths are relative to the working directory**, not the notebook location: `./test_data/input/test_input.tif` and `./test_data/output/test_output.png`. The notebook must be launched from the repo root for these paths to resolve.

## Guidance for Editing Notebooks

- The `.ipynb` is plain JSON (`nbformat` 4). Code lives in `cells[].source` as string arrays; edits must preserve the JSON structure and the per-cell `id` fields.
- Cells are interleaved markdown (explanation) and code; maintain this narrative style if adding cells.
- The notebook writes to `test_data/output/test_output.png`. When making functional changes, keep the paired `test_data/input/test_input.tif` / `test_data/output/test_output.png` example valid, since the README explicitly calls out the test-data convention (input + expected output) as a core requirement of the template.

## Repo Template Context (non-obvious)

- This repo is meant to be **forked via GitHub's "Use this template"**, so changes should keep it generic and reusable as a starting point, not tailored to any single paper or analysis.
- The README is the source of truth for the repository's stated purpose (community-developed publishing checklists — see the cited Schmied et al., *Nat Methods* 2023). Preserve its structure and links when editing.
- The `resources/` screenshots are referenced by relative paths in the README; keep them in sync with any README restructuring.
- `.gitignore` covers `.pixi/*` (Pixi environments/cache). `.pixi/` additionally carries its own ignore file. `.idea/` and `.crush/` are covered by their own per-directory ignore files.
- **Trello integration** (`.github/workflows/trello.yml`) uses the `dalezak/github-commit-to-trello-card` action. It links commits/PRs to Trello cards via a `#<card-number>` pattern in the commit message. It requires three repository secrets — `TRELLO_KEY`, `TRELLO_TOKEN`, `TRELLO_BOARD` — which are per-user and never committed. The workflow will fail on `push` until those secrets exist, so its absence is expected in a fresh fork.

## Repo Cleanup To-Do

Known issues to resolve when cleaning up this repository (each is already flagged above, consolidated here as a checklist):

- [x] **Fix stale README step** — Step 3 of the README references `zebrafish_age_estimator.ipynb`, which does not exist. Point it at `segment_image.ipynb` (matching the Binder link and every other reference).
- [x] **Add a `.gitignore`** — covers `.pixi/*`; `.idea/` and `.crush/` have their own per-directory ignore files.
- [x] **Document the Pixi path in the README** — added a "Use Pixi instead of conda" alternative section with `pixi install` / `pixi run jupyter notebook ...`.
- [x] **Keep `requirements.txt` and Pixi in sync** — added `jupyter` to `requirements.txt` so the conda path no longer hits `jupyter: command not found`.
- [ ] **Verify Binder still works** — the badge/links depend on the cell IDs and notebook path; Binder uses `requirements.txt` (not Pixi), so any change to it must keep `matplotlib` + `scikit-image` + (implicitly) a notebook kernel working.
- [ ] **Align test-data convention** — confirm `test_data/output/test_output.png` still matches what the notebook produces after any functional changes (README calls out this input/output pairing as a core requirement).
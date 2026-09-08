# AGENTS.md

## What This Repository Is

This is `CALM_Template`, a **GitHub template repository** maintained by the Francis Crick Institute. Its purpose is to give researchers a starting point for publishing reusable image-analysis code (ImageJ/FIJI macros, CellProfiler pipelines, ilastik projects, Jupyter notebooks, Python/MATLAB scripts, etc.).

It is **not** a conventional application. There is no build system, test suite, package, framework, or source code beyond a single demo notebook. There is one optional GitHub Actions workflow for Trello integration. Agents should treat it as documentation + a working example, not as software to extend with feature logic.

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

The project supports **two environment toolchains side by side** — Pixi and conda/pip. Do not remove either one.

### Pixi (`pixi.toml`)

- **Python `>=3.14.7,<3.15`** via the `conda-forge` channel.
- Conda deps: `pixi-pycharm` (pulling in a bundled PyCharm for editing the notebook).
- PyPI deps: `matplotlib`, `scikit-image`, `jupyter`.
- `pixi.lock` is a generated lockfile (marked as generated/binary in `.gitattributes`); never hand-edit it.
- `pixi.toml` declares `platforms = ["win-64"]` — the environment is currently Windows-only in practice.

### conda/pip (`requirements.txt`)

- Lists `matplotlib`, `scikit-image`, and `jupyter`.
- Targets the same Python 3.14 (see the README badge).

Do not add runtime dependencies casually; the template is meant to stay lightweight. If a dependency is genuinely needed, add it to both manifests: the correct section of `pixi.toml` (`[dependencies]` for conda, `[pypi-dependencies]` for pip) plus `requirements.txt`, then regenerate `pixi.lock`.

## Key Commands

There is no build, test, or lint suite. The workflow is interactive Jupyter, launched from the repo root (see paths note below).

### Pixi

```bash
pixi install                                    # install deps (first time / after pixi.toml changes)
pixi run jupyter notebook segment_image.ipynb   # launch the notebook
pixi run <task>                                 # run a task from pixi.toml [tasks] (none defined yet)
```

### conda

```bash
conda create --name calm_template pip
conda activate calm_template
python -m pip install -r requirements.txt
jupyter notebook segment_image.ipynb
```

The notebook is also runnable via Binder (link in the README badge), which uses `requirements.txt`, not Pixi.

## Conventions and Gotchas

- **Python 3.14 across both toolchains** (Pixi `conda-forge` and conda/`requirements.txt`), matching the README badge.
- **Two supported environment toolchains coexist**: Pixi (`pixi.toml` + `pixi.lock`) and conda + `requirements.txt`. Both are intentional; do not remove either one.
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
- [x] **Verify Binder still works** — `requirements.txt` now includes `jupyter` (needed for a notebook kernel); the Binder/README/nbconvert notebook path is uniformly `segment_image.ipynb`; input/output data files exist and match the notebook's declared paths.
- [x] **Align test-data convention** — confirmed `test_data/input/test_input.tif` and `test_data/output/test_output.png` exist and match the paths declared in `segment_image.ipynb`.
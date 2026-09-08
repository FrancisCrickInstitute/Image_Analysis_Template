# AGENTS.md

## What This Repository Is

This is `CALM_Template`, a **GitHub template repository** maintained by the Francis Crick Institute. Its purpose is to give researchers a starting point for publishing reusable image-analysis code (ImageJ/FIJI macros, CellProfiler pipelines, ilastik projects, Jupyter notebooks, Python/MATLAB scripts, etc.).

It is **not** a conventional application. There is no build system, test suite, package, framework, CI pipeline, or source code beyond a single demo notebook. Agents should treat it as documentation + a working example, not as software to extend with feature logic.

The only functional artifact is `segment_image.ipynb`: it reads a grayscale TIF, applies Otsu thresholding, displays the original vs. binary image, and saves the binary result as a PNG.

## Project Structure

```
.
├── README.md                 # Primary documentation; describes purpose and setup steps
├── requirements.txt          # Only two packages: matplotlib, scikit-image
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

## Key Commands

There are no build, test, or lint commands. The workflow is interactive Jupyter:

```bash
# One-time environment setup (per README)
conda create --name calm_template pip
conda activate calm_template
python -m pip install -r requirements.txt

# Run the notebook
conda activate calm_template
jupyter notebook segment_image.ipynb
```

The notebook is also runnable via Binder (link in the README badge).

## Conventions and Gotchas

- **Environment is Python 3.11** with `conda` as the recommended distribution. The environment name used throughout the README is `calm_template`.
- **Dependencies are deliberately minimal** (`requirements.txt` has exactly `matplotlib` and `scikit-image`). Do not add runtime dependencies casually; the template is meant to stay lightweight.
- **Notebook paths are relative to the working directory**, not the notebook location: `./test_data/input/test_input.tif` and `./test_data/output/test_output.png`. `jupyter notebook segment_image.ipynb` must be launched from the repo root for these paths to resolve.
- **The notebook's `language_info` metadata is stale**: it reports Python `2.7.6` and `ipython2`, while the project actually targets Python 3.11. This is a known inconsistency in the template; do not "fix" it unless asked, and do not infer the intended Python version from that metadata field.
- **The README references `zebrafish_age_estimator.ipynb`** in Step 3, but the actual file in the repo is `segment_image.ipynb`. The Binder link and the rest of the README correctly point to `segment_image.ipynb`; the Step 3 code block is stale.

## Guidance for Editing Notebooks

- The `.ipynb` is plain JSON (`nbformat` 4). Code lives in `cells[].source` as string arrays; edits must preserve the JSON structure and the per-cell `id` fields.
- Cells are interleaved markdown (explanation) and code; maintain this narrative style if adding cells.
- The notebook writes to `test_data/output/test_output.png`. When making functional changes, keep the paired `test_data/input/test_input.tif` / `test_data/output/test_output.png` example valid, since the README explicitly calls out the test-data convention (input + expected output) as a core requirement of the template.

## Repo Template Context (non-obvious)

- This repo is meant to be **forked via GitHub's "Use this template"**, so changes should keep it generic and reusable as a starting point, not tailored to any single paper or analysis.
- The README is the source of truth for the repository's stated purpose (community-developed publishing checklists — see the cited Schmied et al., *Nat Methods* 2023). Preserve its structure and links when editing.
- The `resources/` screenshots are referenced by relative paths in the README; keep them in sync with any README restructuring.
- The repository has no `.gitignore` and no package metadata beyond `requirements.txt`.
- **Trello integration** (`.github/workflows/trello.yml`) uses the `dalezak/github-commit-to-trello-card` action. It links commits/PRs to Trello cards via a `#<card-number>` pattern in the commit message. It requires three repository secrets — `TRELLO_KEY`, `TRELLO_TOKEN`, `TRELLO_BOARD` — which are per-user and never committed. The workflow will fail on `push` until those secrets exist, so its absence is expected in a fresh fork.

## Repo Cleanup To-Do

Known issues to resolve when cleaning up this repository (each is already flagged above, consolidated here as a checklist):

- [x] **Fix stale README step** — Step 3 of the README references `zebrafish_age_estimator.ipynb`, which does not exist. Point it at `segment_image.ipynb` (matching the Binder link and every other reference).
- [x] **Refresh notebook `language_info` metadata** — `segment_image.ipynb` currently reports Python `2.7.6` / `ipython2` / `pygments_lexer ipython2`. Update to reflect Python 3.11 (target environment per README).
- [ ] **Add a `.gitignore`** — none exists. At minimum ignore Python artifacts (`__pycache__/`, `*.pyc`) and Jupyter runtime caches (`.ipynb_checkpoints/`). Note `.idea/` and `.crush/` are already covered by their own per-directory ignore files.
- [ ] **Pin `requirements.txt` versions** — currently unpinned (`matplotlib`, `scikit-image`). Consider pinning to known-good versions for reproducible Binder launches.
- [ ] **Verify Binder still works** — the badge/links depend on the cell IDs and notebook path; re-run after metadata and README edits.
- [ ] **Align test-data convention** — confirm `test_data/output/test_output.png` still matches what the notebook produces after any functional changes (README calls out this input/output pairing as a core requirement).
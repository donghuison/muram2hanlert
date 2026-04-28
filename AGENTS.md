# Repository Guidelines

## Project Structure & Module Organization

This repository is a lightweight Python bridge from MURaM snapshots to HanleRT input files and PBS jobs. Core modules live at the repository root: `jobs.py` prepares columns, job directories, templates, and submissions; `plot.py` contains diagnostic plotting; `utils.py` provides file helpers; `combine_stokes.py` post-processes HanleRT Stokes output. Campaign entry points live in `launch/` and are named for the run they submit, for example `coolvel.py` and `hotcorona.py`. Runtime templates are `INPUT*.template` and `qsub*.template`. Reference data lives in `Atoms/`, `Molecules/`, and `Resources/`. `notebooks/` is exploratory; generated notebook images are ignored. `jobs/` is for generated run directories and should not collect large outputs in git.

## Build, Test, and Development Commands

There is no Makefile or package metadata. Run commands with the parent directory on `PYTHONPATH` so `import muram2hanlert` resolves:

```bash
PYTHONPATH=$(pwd)/.. python -m py_compile *.py launch/*.py
PYTHONPATH=$(pwd)/.. python launch/coolvel.py 0 1 --print-only
PYTHONPATH=$(pwd)/.. python launch/coolvel.py 0 1 --prepare-only
./combine_stokes.py jobs/<jobname>/iter_<iter>/Y_<yyyy>/Z_<zzzz>
```

Use `--print-only` before submitting a new campaign. Use `--prepare-only` to inspect generated `INPUT`, `hanlert.qsub`, `muram.atmos`, and `muram.field` files before `qsub`. `monitor.sh` is PBS-specific; edit `ITERDIR`, user, and queue assumptions before relying on it.

## Coding Style & Naming Conventions

Use Python 3, four-space indentation, `snake_case` functions, and descriptive campaign names. Preserve existing job path patterns such as `iter_16000/Y_0000/Z_0000` and IDs like `iter_16000_Y_0000_Z_0000`. Keep reusable code in root modules; keep simulation-specific paths, project codes, and sampling choices in `launch/` scripts. Avoid new dependencies unless the workflow actually needs them.

## Testing Guidelines

No dedicated test framework is present. Minimum validation is syntax checking plus a dry run of the affected launch script. For job-generation changes, prepare one small column and inspect the generated atmosphere, field, and PBS files. Do not run bulk `qsub` submissions as a validation shortcut.

## Commit & Pull Request Guidelines

Recent history uses short imperative subjects such as `Add data transfer rsync command`. Keep commits scoped to one workflow or campaign. In PRs, state the affected simulation, launch script, generated path pattern, validation command, and whether any jobs were submitted. Mention changes to project codes, emails, walltime, or queue settings explicitly.

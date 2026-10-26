# Install

Jupyter and Grasshopper run the same code. Start with Jupyter; Grasshopper is optional.
Commands go in *Terminal* on macOS, *PowerShell* on Windows.

## Before you start

| | macOS | Windows |
|---|---|---|
| git | `xcode-select --install` (also the compiler scikit-fmm needs) | [Git for Windows](https://git-scm.com/download/win), default options |
| uv | `curl -LsSf https://astral.sh/uv/install.sh \| sh` | `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 \| iex"` |

Open a new terminal afterwards, so it finds them: `git --version` and `uv --version`
should both answer. uv installs the Python it needs by itself.

## Jupyter

1. Clone compas_vol and this repository into one folder. This repository finds
   compas_vol at `../compas_vol`, so keep that folder name:

   ```bash
   mkdir workshop
   cd workshop
   git clone https://github.com/rccn-dev/compas_vol
   git clone https://github.com/rccn-dev/grid-roof
   ```

   Working on your own copy? Clone that in place of `grid-roof` ([your design](your-design.md)).
2. Install, and open the walkthrough:

   ```bash
   cd grid-roof
   uv sync --extra notebook
   uv run jupyter lab walkthrough.ipynb
   ```

   Jupyter opens in your browser. The next time, only the last line is needed, from the
   `grid-roof` folder.

**Check:** run Step 0 (click it, Shift+Enter). It prints what is installed; `skimage` and
`skfmm` should say *yes*. Then *Run › Run All Cells*: it ends without a red error, in about
a minute.

## Grasshopper

Rhino 8 runs its own Python 3.9, so install both packages into it from the clones above.
In the `grid-roof` folder:

macOS:

```bash
~/.rhinocode/py39-rh8/python3.9 -m pip install -e "../compas_vol[meshing,processes]"
~/.rhinocode/py39-rh8/python3.9 -m pip install --no-deps -e .
```

Windows (PowerShell):

```powershell
& "$env:USERPROFILE\.rhinocode\py39-rh8\python.exe" -m pip install -e "..\compas_vol[meshing,processes]"
& "$env:USERPROFILE\.rhinocode\py39-rh8\python.exe" -m pip install --no-deps -e .
```

`--no-deps` stops pip from looking for compas_vol on PyPI: it is already installed from the
clone. `-e` keeps both editable, so changes in the clones reach Rhino after a restart. If
the Rhino Python path is missing, start Rhino 8 once and open the *ScriptEditor*: it is
created then.

Then the components:

1. Download `compas_vol-ghuser.zip` from the
   [compas_vol releases](https://github.com/rccn-dev/compas_vol/releases) and
   `grid-roof-ghuser.zip` from the [grid-roof releases](https://github.com/rccn-dev/grid-roof/releases).
2. Unzip both into Grasshopper's User Object folder (*File › Special Folders › User Object Folder*).
3. Restart Rhino.

**Check:** **Volumetric Info** on the *COMPAS Vol* tab lists what is installed, and a
*Grid Roof* tab holds five components.

## Trouble

| Symptom | Fix |
|---|---|
| `git` or `uv`: command not found | open a new terminal; on Windows, sign out and in again |
| `uv sync` cannot find `compas-vol` | clone compas_vol next to this folder, named `compas_vol` |
| scikit-fmm fails to build on macOS | `xcode-select --install`, then `uv sync --extra notebook` again |
| `MemoryError`, or the computer stalls while building a piece | close other programs; build at a coarser voxel (`--voxel 16`) |
| Jupyter: `ModuleNotFoundError: grid_roof` | start Jupyter with `uv run` from the `grid-roof` folder |
| a component: *Error getting GH_ScriptInstance instance* | install the latest release of the components |
| a component cannot import `grid_roof` or `compas_vol` | install into Rhino's Python as above, not the terminal's, then restart Rhino |

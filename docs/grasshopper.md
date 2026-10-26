# Grasshopper

Five components on a *Grid Roof* tab run the same steps as the notebook. Install them with
[install](install.md#grasshopper).

## Components

| Component | Inputs | Outputs |
|---|---|---|
| **Roof Brief** | `file`, and nine numbers: `beam`, `beam_depth`, `skin`, `pore`, `fraction_soffit`, `opening_flare`, `planter_depth`, `panel`, `voxel` | `brief`, `numbers` |
| **Roof Drawing** | `file`, `openings` (curves), `planters` (curves), `drains` (points) | `drawing`, `outlines` |
| **Roof Design** | `brief`, `drawing`, `run` | `roof`, `design`, `bounds`, `pieces`, `names`, `report` |
| **Roof Piece** | `roof`, `brief`, `name`, `voxel`, `run` | `mesh`, `report` |
| **Roof Print** | `roof`, `brief`, `name`, `scale`, `printer`, `file`, `run` | `mesh`, `report` |

Unwired inputs keep the defaults: the brief's numbers, the sample drawing.

## On the canvas

```
sliders ─────────► Roof Brief ──┬──────────────────────────────┐
curves, points ──► Roof Drawing ─┴► Roof Design ─ design, bounds ─► Volumetric Mesh
                                         └─ roof, names ──────────► Roof Piece ─► mesh
                                                                    Roof Print ─► STL for PrusaSlicer
```

1. **Roof Brief**: wire number sliders to the values you want to change.
2. **Roof Drawing**: draw closed curves and points in Rhino, in mm, on the 18 × 12 m plan
   with its origin at a corner, and set them as the inputs. `outlines` shows what the roof
   reads.
3. **Roof Design**: runs steps 3 to 10 in about 7 seconds. Wire `design` and `bounds` into
   **Volumetric Mesh** (*COMPAS Vol › Meshing*) to see it. A `resolution` of 100 is half a
   million triangles, in a second or two; much more is slow to hand to Rhino.
   `report` holds the checks for water, light and the kit.
4. **Roof Piece**: pick a piece by name from `names` (a **List Item** works). About 4
   seconds at 40 mm. Bake `mesh` to export it.
5. **Roof Print**: the same piece as a sample at 1:`scale` for the Prusa, about three
   minutes for a panel. Give `file` a path ending in `.stl` and slice it ([print](print.md)).
   Keep `run` False until the design is settled.

Set `run` to `False` with a **Boolean Toggle** to hold a result while you change things
upstream.

## Saving the drawing

To use a drawing from Rhino in the notebook or `build.py`, save it from a Python 3
**Script** component wired to `drawing`:

```python
drawing.save(r"C:\path\to\grid-roof\drawing.json")
```

## Going further

Roof Design runs every step at once. To change one step, call it from a Python 3
**Script** component: `import grid_roof as roof`, then for example
`roof.soffit(brief, slab, beams)`. The functions are listed in [workflow](workflow.md).

# Your design

The roof is a template: a brief, a drawing, a few steps, and a check for every rule. A
design of your own keeps that shape.

## Start a copy

1. On [grid-roof](https://github.com/rccn-dev/grid-roof), choose **Use this template ›
   Create a new repository**. Make it public, under your account or your team's.
2. Clone your copy next to compas_vol, as in [install](install.md), and run the
   walkthrough once, unchanged, to see that it works.

Keep the package name `grid_roof`, so the notebook, the scripts and the Grasshopper
components keep working. Rewrite the README's first paragraph for your design.

## Change it, in this order

| | Where | What |
|---|---|---|
| 1 | `grid_roof/brief.py` | your numbers and their defaults |
| 2 | `drawing.json` | your outlines and points: draw them in Rhino with **Roof Drawing** and save ([grasshopper](grasshopper.md#saving-the-drawing)), or edit the numbers |
| 3 | `grid_roof/design.py` | change a step, or add one: a step takes the brief and earlier results, and returns its own |
| 4 | `grid_roof/checks.py` | a check for every rule you add |
| 5 | `tests/` | a test that the rule holds |

Change one thing, rerun the notebook from that step, look, run the tests, then commit.

## Example: a new rule

*Planters keep 1 m from the roof's edge.* Three edits and a test.

In `grid_roof/brief.py`, a number for the rule, under the planter fields:

```python
    planter_margin: float = 1000.0      # planters keep this far from the roof's edge
```

In `grid_roof/checks.py`, a check that measures it, and its name in `__all__`:

```python
__all__ = ["clear_apertures", "beam_depth", "planter_margins"]


def planter_margins(brief, drawing):
    """For each planter: how close its outline comes to the roof's edge, in mm."""
    margins = []
    for outline in drawing.planters:
        xs = [p[0] for p in outline]
        ys = [p[1] for p in outline]
        margins.append(min(min(xs), min(ys), brief.length - max(xs), brief.width - max(ys)))
    return margins
```

In `grid_roof/__init__.py`, add `planter_margins` to the `from .checks import` line and
to `__all__`. Then a test, at the end of `tests/test_design.py`:

```python
def test_planters_keep_their_margin(brief, drawing):
    assert min(roof.planter_margins(brief, drawing)) >= brief.planter_margin
```

```bash
uv run --group dev python -m pytest -k margin
```

It passes: the sample planters are 1940 to 3528 mm from the edge. Set `planter_margin`
to 2000 and it fails: one planter is 1940 mm away. In the notebook, print the check
in Step 9: `roof.planter_margins(brief, drawing)`.

## What to commit

Commit `brief.json`, `drawing.json`, the notebook and your code. Leave out `out/`,
`prints/` and STLs; `.gitignore` already does.

```bash
git add -A
git commit -m "Planters keep 1 m from the edge"
git push
```

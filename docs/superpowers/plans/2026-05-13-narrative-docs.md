# Narrative Documentation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a narrative documentation guide to the ipydatagrid repo as five Markdown files in `docs/`, linked from the README.

**Architecture:** Each doc page is a standalone Markdown file targeting a single topic area. Pages use prose explanations alongside short, accurate code snippets drawn from the existing example notebooks and Python source. No documentation toolchain (Sphinx, MkDocs) is introduced — GitHub's native Markdown rendering is the only requirement.

**Tech Stack:** Markdown, Python code snippets verified against `ipydatagrid/datagrid.py` and `ipydatagrid/cellrenderer.py`

---

### Task 1: overview.md

**Files:**
- Create: `docs/overview.md`

- [ ] **Step 1: Write `docs/overview.md`**

```markdown
# ipydatagrid Overview

ipydatagrid is a Jupyter widget that renders a pandas DataFrame as an interactive data grid. It is built on [lumino DataGrid](https://github.com/jupyterlab/lumino/tree/main/packages/datagrid) and integrates with ipywidgets for two-way data binding.

## The DataGrid widget

The central object is `DataGrid`. You create one by passing a pandas DataFrame:

```python
import pandas as pd
from ipydatagrid import DataGrid

df = pd.DataFrame({"A": [1, 2, 3], "B": [4, 5, 6]})
grid = DataGrid(df)
grid
```

The grid renders inline in JupyterLab or Jupyter Notebook. Display properties (row height, column width, header visibility) are set at construction time or updated on the live widget.

## Extension points

ipydatagrid has four main extension points:

**Renderers** control how individual cells are displayed. The default renderer shows text, but you can assign different renderers per column — bar charts, hyperlinks, custom icons, HTML, and more. Renderers can also use Vega expressions to change color, font, or content conditionally based on cell values.

**Transforms** filter and sort the visible data without modifying the underlying DataFrame. You apply a list of filter and sort operations to the grid; the grid updates instantly and you can revert to the original view at any time.

**Selections** let users click to highlight rows, columns, or individual cells. Selection mode is configurable, selections can be set programmatically, and selected cell values are accessible from Python.

**Styling** controls the visual appearance of the grid as a whole: background colors, grid line colors, header colors, cursor and selection highlight colors, and column widths. Styles are set via a `grid_style` dictionary whose values can be static colors or dynamic Vega expressions.

## What's covered in this guide

- [Getting Started](getting-started.md) — creating a grid and setting basic options
- [Renderers](renderers.md) — customizing cell display and conditional formatting
- [Interactivity](interactivity.md) — selections and filtering/sorting
- [Styling](styling.md) — themes, colors, and column widths

For runnable examples of all features, see the [examples/](../examples/) directory.
```

- [ ] **Step 2: Commit**

```bash
git add docs/overview.md
git commit -m "docs: add overview.md"
```

---

### Task 2: getting-started.md

**Files:**
- Create: `docs/getting-started.md`

- [ ] **Step 1: Write `docs/getting-started.md`**

```markdown
# Getting Started

## Installation

```bash
pip install ipydatagrid
```

## Creating a grid

Pass any pandas DataFrame to `DataGrid`:

```python
import pandas as pd
from ipydatagrid import DataGrid

df = pd.DataFrame({
    "Name": ["Alice", "Bob", "Carol"],
    "Score": [92, 85, 78],
    "Passed": [True, True, True],
})

grid = DataGrid(df)
grid
```

The index column is included in the grid by default. To give it a custom label, pass `index_name`:

```python
grid = DataGrid(df, index_name="ID")
```

## Key constructor options

| Parameter | Default | Description |
|---|---|---|
| `base_row_size` | `20` | Row height in pixels |
| `base_column_size` | `64` | Default column width in pixels |
| `base_row_header_size` | `64` | Index column width in pixels |
| `base_column_header_size` | `20` | Column header height in pixels |
| `header_visibility` | `'all'` | Which headers to show: `'all'`, `'row'`, `'column'`, or `'none'` |
| `selection_mode` | `'none'` | How clicks select cells: `'cell'`, `'row'`, `'column'`, or `'none'` |
| `editable` | `False` | Whether cells can be edited directly in the grid |

Example using several of these:

```python
grid = DataGrid(
    df,
    base_row_size=30,
    base_column_size=120,
    header_visibility="column",
    selection_mode="row",
)
```

## Assigning renderers

The `default_renderer` applies to all columns that don't have a specific renderer assigned. The `renderers` dict maps column names to renderer instances.

```python
from ipydatagrid import DataGrid, TextRenderer

grid = DataGrid(
    df,
    default_renderer=TextRenderer(text_color="black", background_color="white"),
    renderers={
        "Score": TextRenderer(text_color="navy", font="bold 13px sans-serif"),
    },
)
```

See [Renderers](renderers.md) for the full list of renderer types and options.
```

- [ ] **Step 2: Commit**

```bash
git add docs/getting-started.md
git commit -m "docs: add getting-started.md"
```

---

### Task 3: renderers.md

**Files:**
- Create: `docs/renderers.md`

- [ ] **Step 1: Write `docs/renderers.md`**

```markdown
# Renderers

Renderers control how cells are displayed in the grid. ipydatagrid ships several renderer types, all importable from the top-level `ipydatagrid` package.

## Assigning renderers

Use `default_renderer` to set a fallback renderer for all columns, and the `renderers` dict to override specific columns by name. `header_renderer` and `corner_renderer` control the column headers and corner cell respectively.

```python
from ipydatagrid import DataGrid, TextRenderer

grid = DataGrid(
    df,
    default_renderer=TextRenderer(background_color="#f5f5f5"),
    renderers={
        "Status": TextRenderer(text_color="green", font="bold 13px sans-serif"),
    },
    header_renderer=TextRenderer(background_color="#ddd", text_color="black"),
)
```

You can update a renderer after the grid is displayed and the grid will re-render immediately:

```python
grid.renderers["Status"].background_color = "red"
```

## TextRenderer

The most common renderer. Displays text with configurable font, color, and alignment.

```python
from ipydatagrid import TextRenderer

TextRenderer(
    text_color="black",
    background_color="white",
    font="12px sans-serif",
    horizontal_alignment="left",  # 'left', 'center', 'right'
    format=".2f",                  # Python format string, e.g. for numbers
    text_wrap=False,               # wrap long text
)
```

## BarRenderer

Renders a horizontal bar chart inside the cell. Useful for numeric columns.

```python
from bqplot import LinearScale, ColorScale
from ipydatagrid import DataGrid, BarRenderer

bar_renderer = BarRenderer(
    bar_color=ColorScale(min=0, max=100, scheme="viridis"),
    bar_value=LinearScale(min=0, max=100),
    bar_horizontal_alignment="center",
    show_text=False,
)

grid = DataGrid(df, renderers={"Score": bar_renderer})
```

`bar_value` maps the cell value to a bar width (a bqplot `Scale`). `bar_color` maps the value to a fill color.

## HyperlinkRenderer

Renders a clickable hyperlink. The cell value is expected to be a list of `[url, label]`.

```python
from ipydatagrid import DataGrid, HyperlinkRenderer, VegaExpr

link_renderer = HyperlinkRenderer(
    url=VegaExpr("cell.value[0]"),
    url_name=VegaExpr("cell.value[1]"),
    text_color="blue",
    font="bold 14px Arial, sans-serif",
)

df = pd.DataFrame({
    "Name": ["NumFocus"],
    "Link": [["https://numfocus.org/", "Better tools to build a better world"]],
})

grid = DataGrid(df, renderers={"Link": link_renderer})
```

## BoolRenderer (via TextRenderer + FontAwesome)

There is no dedicated `BoolRenderer` class. Boolean-style rendering is achieved with `TextRenderer` using `text_value` (a Vega expression that returns a character) and a FontAwesome font:

```python
from ipydatagrid import DataGrid, TextRenderer, Expr

def icon(cell):
    return "" if cell.value else ""  # ✓ or ✗

def color(cell):
    return "#2fbd34" if cell.value else "#b82538"

renderer = TextRenderer(
    font="bold 14px fontawesome",
    text_value=Expr(icon),
    text_color=Expr(color),
    horizontal_alignment="center",
)
```

For the full FontAwesome icon reference, see https://fontawesome.com/v4.7.0/cheatsheet/.

## HtmlRenderer

Renders raw HTML inside a cell. Use with care — content is injected directly into the DOM.

```python
from ipydatagrid import DataGrid, HtmlRenderer

grid = DataGrid(df, default_renderer=HtmlRenderer())
```

The cell value should be an HTML string, e.g. `"<b>hello</b>"`.

## ImageRenderer

Renders an image inside a cell. The cell value should be a URL or a base64-encoded data URI.

```python
from ipydatagrid import DataGrid, ImageRenderer

grid = DataGrid(df, renderers={"Photo": ImageRenderer()})
```

## Conditional formatting with Vega expressions

Any renderer property that accepts a color, font, or string can also accept a `VegaExpr` or `Expr` for dynamic, value-based rendering.

**`VegaExpr`** takes a JavaScript/Vega expression string. The expression has access to `cell` (with `.value`, `.row`, `.column`, `.metadata`) and `default_value`.

**`Expr`** takes a Python function. The function receives a `cell` object and returns a value. It is compiled to a Vega expression at runtime.

```python
from ipydatagrid import DataGrid, TextRenderer, Expr, VegaExpr

# Using a Python function (Expr)
def highlight_negative(cell):
    return "salmon" if cell.value < 0 else "white"

renderer = TextRenderer(background_color=Expr(highlight_negative))

# Using a Vega expression string (VegaExpr)
renderer = TextRenderer(
    background_color=VegaExpr("cell.value < 0 ? 'salmon' : 'white'")
)
```

Vega expressions can also reference sibling column values via `cell.metadata.data`:

```python
def color_based_on_sibling(cell):
    return "green" if cell.metadata.data["Signal"] == "Buy" else "red"

renderer = TextRenderer(background_color=Expr(color_based_on_sibling))
```

You can update a `VegaExpr` or `Expr` value on a live grid and it will re-render:

```python
renderer.background_color.value = "cell.value < 0 ? 'red' : 'white'"
```
```

- [ ] **Step 2: Commit**

```bash
git add docs/renderers.md
git commit -m "docs: add renderers.md"
```

---

### Task 4: interactivity.md

**Files:**
- Create: `docs/interactivity.md`

- [ ] **Step 1: Write `docs/interactivity.md`**

```markdown
# Interactivity: Selections and Filtering

## Selections

### Selection modes

Set `selection_mode` at construction time (or update it on the live grid):

```python
from ipydatagrid import DataGrid

grid = DataGrid(df, selection_mode="cell")  # 'cell', 'row', 'column', or 'none'
```

| Mode | Behavior |
|---|---|
| `'cell'` | Individual cell selection |
| `'row'` | Clicking any cell selects the whole row |
| `'column'` | Clicking any cell selects the whole column |
| `'none'` | Selection disabled |

### Programmatic selection

```python
# Select a rectangle from (row1, col1) to (row2, col2)
grid.select(row1=1, column1=1, row2=2, column2=2, clear_mode="all")

# Select a single cell
grid.select(4, 1)

# Add to existing selection (default clear_mode is "none")
grid.select(2, 2, 3, 3)

# Clear all selections
grid.clear_selection()
```

`clear_mode` controls what happens to existing selections:
- `"none"` (default): add to existing selections
- `"all"`: replace all existing selections
- `"current"`: replace only the most recent selection

You can also set the `selections` traitlet directly as a list of rectangle dicts:

```python
grid.selections = [
    {"r1": 0, "c1": 0, "r2": 2, "c2": 1},
]
```

### Reading selections

```python
# List of dicts with 'r' and 'c' keys
grid.selected_cells

# List of cell values in selection order
grid.selected_cell_values

# Iterate over selected cells one by one
for cell in grid.selected_cell_iterator:
    print(cell)  # {'r': 0, 'c': 0}

# Count selected cells
len(grid.selected_cell_iterator)
```

`selected_visible_cell_iterator` iterates over selected cells in the currently filtered/sorted view.

## Filtering and sorting

Use `transform()` to apply filter and sort operations. Transforms update the visible rows without modifying the underlying DataFrame.

```python
grid.transform([
    {"type": "filter", "operator": "=", "column": "Origin", "value": "Europe"},
    {"type": "sort", "column": "Horsepower", "desc": True},
])
```

Multiple filters can be combined:

```python
grid.transform([
    {"type": "filter", "operator": "=", "column": "Origin", "value": "USA"},
    {"type": "filter", "operator": "<", "column": "Horsepower", "value": 130},
    {"type": "sort", "column": "Acceleration"},
])
```

Supported filter operators: `"="`, `"<"`, `">"`, `"<="`, `">="`, `"!="`.

Revert all transforms to restore the original view:

```python
grid.revert()
```

### Reading the filtered view

`get_visible_data()` returns the currently visible rows as a DataFrame:

```python
filtered_df = grid.get_visible_data()
```
```

- [ ] **Step 2: Commit**

```bash
git add docs/interactivity.md
git commit -m "docs: add interactivity.md"
```

---

### Task 5: styling.md

**Files:**
- Create: `docs/styling.md`

- [ ] **Step 1: Write `docs/styling.md`**

```markdown
# Styling

## Grid style

The `grid_style` property accepts a dict of color values that control the grid's visual appearance. All color values accept any valid CSS color string.

```python
monokai = {
    "background_color": "#2c292d",
    "grid_line_color": "#a698eb7a",
    "header_background_color": "#2c292d9a",
    "header_grid_line_color": "#fc98675a",
    "selection_fill_color": "#78dce81a",
    "selection_border_color": "#ffd866",
    "header_selection_fill_color": "#ab9df24a",
    "header_selection_border_color": "lawngreen",
    "cursor_fill_color": "#78dce87a",
    "cursor_border_color": "#ff6188",
}

grid.grid_style = monokai
```

You can set `grid_style` at construction or update it on a live grid.

### Supported style properties

| Property | Description |
|---|---|
| `background_color` | Background color for all body cells |
| `void_color` | Color of the area outside the grid |
| `grid_line_color` | Color of both vertical and horizontal grid lines |
| `vertical_grid_line_color` | Vertical grid line color only |
| `horizontal_grid_line_color` | Horizontal grid line color only |
| `header_background_color` | Background color for header cells |
| `header_grid_line_color` | Grid line color for header cells |
| `header_vertical_grid_line_color` | Vertical grid line color for headers |
| `header_horizontal_grid_line_color` | Horizontal grid line color for headers |
| `selection_fill_color` | Fill color of selected area |
| `selection_border_color` | Border color of selected area |
| `header_selection_fill_color` | Fill color of headers in a selection |
| `header_selection_border_color` | Border color of headers in a selection |
| `cursor_fill_color` | Fill color of the cursor cell |
| `cursor_border_color` | Border color of the cursor cell |
| `row_background_color` | Row-wise background color (string or VegaExpr) |
| `column_background_color` | Column-wise background color (string or VegaExpr) |
| `scroll_shadow` | Dict with `size`, `color1`, `color2`, `color3` for scroll shadow |

`row_background_color` and `column_background_color` accept a `VegaExpr` for alternating or dynamic coloring:

```python
from ipydatagrid import DataGrid, VegaExpr

grid.grid_style = {
    "column_background_color": VegaExpr(
        "cell % 2 === 0 ? 'rgb(255, 130, 145, 0.5)' : 'rgb(255, 130, 145, 0.2)'"
    )
}
```

## Stripes

Enable alternating row or column shading using the theme's built-in stripe colors:

```python
grid = DataGrid(df, horizontal_stripes=True)
grid = DataGrid(df, vertical_stripes=True)
```

These can also be set on a live grid:

```python
grid.horizontal_stripes = True
```

## Cell sizes

Control default row and column dimensions at construction time:

```python
grid = DataGrid(
    df,
    base_row_size=30,           # row height in pixels (default: 20)
    base_column_size=120,       # column width in pixels (default: 64)
    base_row_header_size=80,    # index column width in pixels (default: 64)
    base_column_header_size=24, # column header height in pixels (default: 20)
)
```

## Column widths

Set widths for specific columns using the `column_widths` dict:

```python
grid = DataGrid(df, column_widths={"Name": 200, "Score": 80})
```

### Auto-fit

Enable automatic column width sizing based on content:

```python
grid.auto_fit_columns = True
```

Control how auto-fit behaves with `auto_fit_params`:

```python
grid.auto_fit_params = {
    "area": "body",   # 'row-header', 'body', or 'all' (default)
    "padding": 60,    # extra pixels added to fitted width (default: 30)
    "numCols": 1,     # cap number of columns to auto-fit (default: None = all)
}
grid.auto_fit_columns = True
```
```

- [ ] **Step 2: Commit**

```bash
git add docs/styling.md
git commit -m "docs: add styling.md"
```

---

### Task 6: Update README

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Add Documentation section to README**

Find the `## Usage and Examples` section in `README.md`. After the closing paragraph of that section (the line `Tutorial and example notebooks can be found in the `/examples` directory.`), insert:

```markdown
## Documentation

- [Overview](docs/overview.md) — how ipydatagrid works
- [Getting Started](docs/getting-started.md)
- [Renderers](docs/renderers.md)
- [Interactivity: Selections & Filtering](docs/interactivity.md)
- [Styling](docs/styling.md)
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add Documentation section to README"
```

---

### Task 7: Clean up internal tooling before PR

**Files:**
- Delete: `docs/superpowers/` (entire directory)

- [ ] **Step 1: Remove the superpowers directory**

```bash
git rm -r docs/superpowers/
```

- [ ] **Step 2: Verify only the four doc files and README change remain**

```bash
git diff upstream/main --name-only
```

Expected output:
```
README.md
docs/overview.md
docs/getting-started.md
docs/renderers.md
docs/interactivity.md
docs/styling.md
```

- [ ] **Step 3: Commit**

```bash
git commit -m "chore: remove internal planning docs before PR"
```

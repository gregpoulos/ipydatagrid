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

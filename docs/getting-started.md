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

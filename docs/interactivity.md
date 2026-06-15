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

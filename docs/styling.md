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

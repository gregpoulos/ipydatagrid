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
import pandas as pd
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

## Icon rendering with FontAwesome (via TextRenderer)

There is no dedicated `BoolRenderer` class. Icon-style rendering is achieved with `TextRenderer` by setting `text_value` to a Vega expression that returns a FontAwesome character and `font` to `"bold 14px fontawesome"`:

```python
from ipydatagrid import DataGrid, TextRenderer, Expr

def icon(cell):
    return "" if cell.value else ""  # U+F00C = check, U+F00D = cross

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

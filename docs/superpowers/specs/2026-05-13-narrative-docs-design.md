# Design: Narrative Documentation for ipydatagrid

**Date:** 2026-05-13
**Author:** Gregory Poulos
**Status:** Approved

## Goal

Add a narrative documentation guide to the ipydatagrid GitHub repo to help new users understand the library before diving into the example notebooks. The guide lives in the repo itself (no separate hosted site) to stay lightweight and avoid confusion with the broader Jupyter Widgets documentation ecosystem.

## Structure

Five Markdown files in a new `docs/` directory:

```
docs/
├── overview.md
├── getting-started.md
├── renderers.md
├── interactivity.md
└── styling.md
```

The `docs/superpowers/` subdirectory is internal tooling and must be deleted before the PR is opened. It should not appear in `.gitignore` or anywhere in the submitted diff.

### overview.md

Prose-only concept map — no code. Explains:
- What `DataGrid` is and its relationship to pandas DataFrames
- The four main extension points: **renderers**, **transforms**, **selections**, and **editing**
- How these compose (e.g. renderers are per-column display logic; transforms are server-side filter/sort; selections drive interactivity)
- Brief mentions of styling, hierarchical data, and streaming so users know these exist
- Links to the other three pages for deeper coverage

This is the "read this first" page. Its job is to give users a mental model before they look at any code.

### getting-started.md

Covers the basics with short, working code snippets:
- Creating a `DataGrid` from a pandas DataFrame
- Key constructor arguments (`base_row_size`, `header_visibility`, `selection_mode`, `editable`)
- Assigning a `default_renderer`
- Assigning per-column renderers via the `renderers` dict
- Displaying the grid in a notebook

References: `DataGrid.ipynb`, `Pandas.ipynb`

### renderers.md

Covers each renderer class and the conditional formatting system:
- `TextRenderer` — text display, font, color, alignment
- `BarRenderer` — bar chart in a cell
- `HyperlinkRenderer` — clickable links
- `BoolRenderer` — checkbox display for boolean columns
- `HtmlRenderer` — raw HTML in a cell
- `ImageRenderer` — image display
- `VegaExpr` / `Expr` — how to use Vega expressions for conditional formatting (color/font based on cell value)
- The `renderers` dict (per-column) vs. `default_renderer` (fallback) vs. `header_renderer`/`corner_renderer`

References: `BoolRenderer.ipynb`, `HtmlRenderer.ipynb`, `HyperlinkRenderer.ipynb`, `ConditionalFormatting.ipynb`, `Text Wrapping and Eliding.ipynb`

### interactivity.md

Covers selections and filtering/transforms:
- Selection modes: `'row'`, `'column'`, `'cell'`, `'none'`
- Programmatic selection: `select()`, `clear_selection()`
- Reading selections: `selected_cells`, `selected_cell_values`, `SelectionHelper`
- Transforms: applying filter and sort transforms via `transform()`, reverting with `revert()`
- `get_visible_data()` — reading the current filtered/sorted view as a DataFrame

References: `Selections.ipynb`

### styling.md

Covers visual customization:
- Themes: using the built-in light/dark themes
- `grid_style` dict: background colors, grid line colors, header colors, selection colors, cursor colors, scroll shadow
- `horizontal_stripes` and `vertical_stripes`
- Column widths: `column_widths` dict, `auto_fit_columns`, `auto_fit_params`
- `base_row_size`, `base_column_size`, `base_row_header_size`, `base_column_header_size`

References: `Themes.ipynb`, `Column Width Auto-Fit.ipynb`

## README change

Add a short "Documentation" section after the existing "Usage and Examples" section:

```markdown
## Documentation

- [Overview](docs/overview.md) — how ipydatagrid works
- [Getting Started](docs/getting-started.md)
- [Renderers](docs/renderers.md)
- [Interactivity: Selections & Filtering](docs/interactivity.md)
- [Styling](docs/styling.md)
```

## Out of scope

The following topics are acknowledged in `overview.md` but do not get dedicated pages in this initial pass:
- Editable mode and cell change callbacks
- Hierarchical data (nested hierarchies, trees)
- Streaming / live data updates

These can be added as separate files later.

## Contribution path

The docs are authored on a fork of `jupyter-widgets/ipydatagrid` and submitted as a PR. No hosted documentation site is created — GitHub's native Markdown rendering is sufficient.

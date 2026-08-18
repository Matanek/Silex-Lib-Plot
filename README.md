# Plot

Plot turns Silex data into charts without exposing a graphics pipeline. It is
aimed at scientific computing, AI experiments, benchmarks, finance, and the
other workflows where visualization should stay simpler than the problem being
studied. GFX provides the native vector presentation; Plot can also emit
standalone SVG documents for reports, publications, and headless workflows.

```text
silex install Plot
```

```sx
use Plot

let x:float[] = [0.0, 1.0, 2.0, 3.0]
let y:float[] = [0.0, 1.0, 4.0, 9.0]

var figure = Plot.line(x, y)
    ..title("Croissance quadratique")
    ..x_label("Temps")
    ..y_label("Valeur")

figure.show()
```

Compose several marks in one chart before displaying or exporting the figure:

```sx
var figure = Plot.line(x, expected, "Modèle")
    ..scatter(x, measured, "Mesures")

match figure.save_svg("comparison.svg") {
    failure(error) => { panic(error.operation + ": " + error.detail) }
    success => {}
}
```

A line describes a continuous model or an interpolation between its own
values. A scatter series describes discrete observations and therefore does
not imply a connection between them. The legend uses the corresponding line
or point symbol so this distinction remains visible.

## Common charts

The top-level functions create a one-chart `Figure`, which can be configured
with Silex cascades:

```sx
let distribution = Plot.histogram(samples, 30, "Latence")
let comparison = Plot.bar(["CPU", "GPU"], [82.0, 19.0], "ms")
let summary = Plot.box_plot(before, "Avant")..box_plot(after, "Après")
```

Plot supports:

- `line(x, y)` and `scatter(x, y)` for models, signals, and observations;
- `bar(categories, values)` and `histogram(values, bins)` for comparisons and
  distributions;
- `heatmap(values, columns)` for matrices, correlations, and confusion data;
- `band(x, lower, upper)` and `error_bars(x, y, lower, upper)` for uncertainty;
- `box_plot(values)` for robust distribution summaries;
- `candlesticks(x, open, high, low, close)` for OHLC market data;
- `horizontal(value)` and `vertical(value)` for thresholds and references;
- `x_log()` and `y_log()` for logarithmic scales;
- `x_decimals(n)` and `y_decimals(n)` when an axis needs an explicit maximum
  number of decimals.

Heatmaps accept a flattened row-major array. The number of rows is inferred
from `values.count() / columns`. Every input is checked for compatible lengths,
finite values, and domain errors before rendering.

## Multiple charts

Use a grid when a report or experiment needs independent axes and chart types:

```sx
var dashboard = Plot.grid(2, 2)
    ..title("Suivi d'entraînement")
    ..size(1280, 800)

dashboard.at(0, 0)
    ..line(epochs, accuracy, "Validation")
    ..title("Précision")

dashboard.at(0, 1)
    ..histogram(errors, 20)
    ..title("Erreurs")

dashboard.at(1, 0)
    ..heatmap(confusion, 3)
    ..x_categories(classes)
    ..y_categories(classes)

dashboard.at(1, 1)
    ..bar(devices, latency)
    ..horizontal(target, "Objectif")

dashboard.show()
```

`Figure` owns the complete window, export, size, and grid. `Chart` owns one
panel and its data, axes, title, and annotations. Rows and columns are
zero-based in `at(row, column)`. Existing single-chart code remains the short,
direct form.

## Legends

Legend layout is automatic by default. A single labeled series does not repeat
its meaning in a legend. A few entries share the title row when space permits;
denser legends move to the right, and narrow panels fall back inside the data
area.

The placement can be chosen explicitly when a report needs a stable layout:

```sx
chart.legend_hidden() // no legend
chart.legend_inline() // horizontal, in the title row
chart.legend_inline_left()
chart.legend_inline_center()
chart.legend_inline_right()
chart.legend_right()  // outside the data area
chart.legend_inside() // overlaid inside the data area
chart.legend_auto()   // restore adaptive placement
```

Each legend entry uses the visual language of its data: line, point, filled
bar, uncertainty band, error bar, box plot, or rising/falling candlestick.
Inline legends use compact content-based spacing. Their default alignment is
centered in the free part of the title row; left and right alignment remain
available for stable report layouts.

Bounds are automatic. Linear scales are the default; logarithmic scales require
strictly positive data. Axis labels are rounded automatically from the visible
tick interval so `float32` representation noise is not displayed. An explicit
decimal setting can be restored to automatic behavior with `x_decimals_auto()`
or `y_decimals_auto()`. Titles, axis labels, category labels, legends, and
reference lines work in both native presentation and SVG export.

The native figure is retained as vector geometry by GFX.Scene2D, fills its
window, and follows the window continuously while it is resized. No chart image
is rasterized for interactive presentation. `size(width, height)` selects the
authored figure, the initial window size, and the deterministic SVG dimensions.

`clear()` removes the data from a chart while preserving its labels and scale
configuration, which makes a chart easy to rebuild from a new data snapshot.

`Plot.Figure` and `Plot.Chart` are the public visualization model. GFX canvases,
viewers, Scene2D entities, histogram bins, quartile computation, and rendering
details remain private implementation choices.

## Development

Register the checkout and run the consumer tests with the current toolchain:

```text
silex link .
silex test Tests
silex compile Examples/Quadratic.sx -o quadratic
silex compile Examples/ScientificDashboard.sx -o scientific-dashboard
```

The examples cover a line/scatter comparison, scientific and AI monitoring,
benchmark reporting, and financial OHLC data.

# Plot

`Plot` turns Silex data into charts without exposing a graphics pipeline.
`GFX.Viewer` provides the native vector presentation for `Figure.show()`;
`save_svg` emits standalone documents for reports and headless workflows.

## First chart

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

Compose several marks before displaying or exporting:

```sx
var figure = Plot.line(x, expected, "Modèle")
    ..scatter(x, measured, "Mesures")

match figure.save_svg("comparison.svg") {
    failure(error) => { panic(error.operation + ": " + error.detail) }
    success => {}
}
```

A line represents a continuous model or interpolation; a scatter series
represents discrete observations. The legend preserves that distinction.

## Chart types

Top-level functions create a one-chart `Figure`. The package supports lines,
scatter series, simple or stacked areas, bars, histograms, heatmaps, uncertainty
bands, error bars, box plots, candlesticks, pies, donuts, gauges, polar bars,
pyramids, funnels, and reference lines.

```sx
let distribution = Plot.histogram(samples, 30, "Latence")
let comparison = Plot.bar(["CPU", "GPU"], [82.0, 19.0], "ms")
let summary = Plot.box_plot(before, "Avant")..box_plot(after, "Après")
```

Inputs are validated before rendering for compatible lengths, finite values,
and valid domains. Heatmaps use a flattened row-major array. `x_log()` and
`y_log()` require strictly positive data.

## Circular charts and palettes

```sx
let share = Plot.donut(
    ["Produit", "Services", "Support"],
    [52.0, 31.0, 17.0]
)
    ..title("Répartition du revenu")
    ..palette(Plot.Palette.ocean())
    ..inner_radius(0.58)
    ..labels_outside()
    ..show_percentages()
```

The `modern`, `ocean`, `sunset`, and `neon` palettes provide coordinated
defaults. `Plot.Palette` also accepts a custom identity. Labels may be hidden,
placed inside, or placed outside. `start_angle`, `slice_gap`, `inner_radius`,
and `explode` control composition.

## Areas and progressions

```sx
let activity = Plot.area(months, product, "Produit")
    ..area(months, services, "Services")
    ..fill_opacity(0.30)
    ..smooth()

let mix = Plot.normalized_area(
    months,
    [product, services, support],
    ["Produit", "Services", "Support"]
)
```

Stacked or normalized areas share their x coordinates and require finite,
non-negative values. Pyramids express levels from foundation to apex; funnels
preserve the stage order supplied by the caller.

## Dashboards

A grid combines charts with independent axes:

```sx
var dashboard = Plot.grid(2, 2)
    ..title("Suivi d'entraînement")
    ..size(1280, 800)

dashboard.at(0, 0)..line(epochs, accuracy, "Validation")
dashboard.at(0, 1)..histogram(errors, 20)
dashboard.at(1, 0)..heatmap(confusion, 3)
dashboard.at(1, 1)..bar(devices, latency)

dashboard.show()
```

`Figure` owns the window, export, size, and grid. `Chart` owns one panel, its
data, axes, and annotations. `at` uses zero-based indexes. `clear()` removes the
data while preserving configuration.

## Legends and rendering

Legend placement is automatic, while `legend_hidden`, `legend_inline`,
`legend_right`, `legend_inside`, and `legend_auto` make it explicit. Each entry
uses the visual symbol of its data.

Bounds and decimal precision are automatic. Titles, axes, categories, legends,
and references are preserved in native presentation and SVG. The figure remains
`GFX.Scene2D` vector geometry, follows window resizing, and is not rasterized.

## Development

```text
silex link Packages/Plot
silex test Packages/Plot/Tests
```

The [Plot gallery](https://github.com/Matanek/Silex-Examples/blob/main/Sources/PlotGallery.sx)
shows the main chart types in one complete example.

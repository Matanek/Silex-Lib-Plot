# Plot

Plot turns Silex numerical data into charts without exposing a graphics
pipeline. It uses GFX privately for native presentation and can emit standalone
SVG documents for reports, publications, and headless workflows.

```text
silex install Plot
```

```sx
use Plot

let x:float[] = [0.0, 1.0, 2.0, 3.0]
let y:float[] = [0.0, 1.0, 4.0, 9.0]

var figure = Plot.line(x, y)
    .title("Croissance quadratique")
    .x_label("Temps")
    .y_label("Valeur")

figure.show()
```

Compose several series before displaying or exporting the figure:

```sx
var figure = Plot.line(x, expected, "Modèle")
    .scatter(x, measured, "Mesures")

match figure.save_svg("comparison.svg") {
    failure(error) => { panic(error.operation + ": " + error.detail) }
    success => {}
}
```

A line describes a continuous model or an interpolation between its own
values. A scatter series describes discrete observations and therefore does
not imply a connection between them. The legend uses the corresponding line
or point symbol so this distinction remains visible.

The initial release provides line and scatter series, automatic linear bounds,
titles, axis labels, legends, native display, and SVG export. The native chart
is retained as vector geometry by GFX.Scene2D, fills its window, and follows
the window continuously while it is resized. No chart image is rasterized for
interactive presentation. `size(width, height)` selects the authored figure,
the initial window size, and the deterministic SVG dimensions.

`Plot.Figure` is the public visualization model; GFX canvases, viewers,
Scene2D entities, and rendering details remain private implementation choices.

## Development

Register the checkout and run the consumer tests with the current toolchain:

```text
silex link .
silex test Tests
silex compile Examples/Quadratic.sx -o quadratic
```

`Quadratic.sx` writes `quadratic.svg` and then opens its native viewer when run.

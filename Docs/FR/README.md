# Plot

`Plot` transforme les données Silex en graphiques sans exposer de pipeline
graphique. `GFX.Viewer` assure la présentation vectorielle native de
`Figure.show()` ; `save_svg` produit des documents autonomes pour les rapports
et les traitements sans interface.

## Premier graphique

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

Composez plusieurs marques avant l’affichage ou l’export :

```sx
var figure = Plot.line(x, expected, "Modèle")
    ..scatter(x, measured, "Mesures")

match figure.save_svg("comparison.svg") {
    failure(error) => { panic(error.operation + ": " + error.detail) }
    success => {}
}
```

Une ligne représente un modèle continu ou une interpolation ; un nuage de
points représente des observations discrètes. La légende conserve cette
distinction.

## Types de graphiques

Les fonctions de premier niveau créent une `Figure` à un graphique. Le package
propose lignes, points, aires simples ou empilées, barres, histogrammes,
heatmaps, bandes d’incertitude, barres d’erreur, box plots, chandeliers, secteurs,
anneaux, jauges, barres polaires, pyramides, entonnoirs et lignes de référence.

```sx
let distribution = Plot.histogram(samples, 30, "Latence")
let comparison = Plot.bar(["CPU", "GPU"], [82.0, 19.0], "ms")
let summary = Plot.box_plot(before, "Avant")..box_plot(after, "Après")
```

Les entrées sont contrôlées avant rendu : longueurs compatibles, valeurs finies
et domaines valides. Les heatmaps utilisent un tableau aplati par lignes.
`x_log()` et `y_log()` imposent des données strictement positives.

## Graphiques circulaires et palettes

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

Les palettes `modern`, `ocean`, `sunset` et `neon` offrent des valeurs
coordonnées. `Plot.Palette` accepte aussi une identité personnalisée. Les
libellés peuvent être masqués, placés dedans ou dehors. `start_angle`,
`slice_gap`, `inner_radius` et `explode` règlent la composition.

## Aires et progressions

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

Les aires empilées ou normalisées partagent leurs coordonnées x et exigent des
valeurs finies positives. Les pyramides expriment des niveaux de la base au
sommet ; les entonnoirs conservent l’ordre des étapes fourni par l’appelant.

## Tableaux de bord

Une grille réunit des graphiques aux axes indépendants :

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

`Figure` possède la fenêtre, l’export, la taille et la grille. `Chart` possède
un panneau, ses données, axes et annotations. Les indices de `at` commencent à
zéro. `clear()` retire les données en conservant la configuration.

## Légendes et rendu

Le placement des légendes est automatique, mais les méthodes `legend_hidden`,
`legend_inline`, `legend_right`, `legend_inside` et `legend_auto` permettent de
le fixer. Chaque entrée reprend le symbole visuel de ses données.

Les bornes et le nombre de décimales sont automatiques. Titres, axes, catégories,
légendes et références sont conservés dans la vue native et le SVG. La figure
reste une géométrie vectorielle `GFX.Scene2D`, suit le redimensionnement de la
fenêtre et n’est pas rasterisée.

## Développement

```text
silex link Packages/Plot
silex test Packages/Plot/Tests
```

La [galerie Plot](https://github.com/Matanek/Silex-Examples/blob/main/Sources/PlotGallery.sx)
présente les principaux types de graphiques dans un exemple complet.

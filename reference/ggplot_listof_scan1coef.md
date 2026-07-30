# Plot of object of class listof_scan1coeff

Plot object of class `listof_scan1coeff`, which is a list of objects of
class `scan1coef`.

## Usage

``` r
ggplot_listof_scan1coef(
  object,
  map,
  columns = NULL,
  col = NULL,
  scan1_output = NULL,
  facet = "pattern",
  ...
)

# S3 method for class 'listof_scan1coef'
autoplot(object, ...)
```

## Arguments

- object:

  object of class `listof_scan1coeff`

- map:

  A list of vectors of marker positions, as produced by
  [`insert_pseudomarkers`](https://rdrr.io/pkg/qtl2/man/insert_pseudomarkers.html).

- columns:

  Vector of columns to plot

- col:

  Vector of colors, same length as `columns`. If NULL, some default
  choices are made.

- scan1_output:

  If provided, we make a two-panel plot with coefficients on top and LOD
  scores below. Should have just one LOD score column; if multiple, only
  the first is used.

- facet:

  Plot facets if multiple phenotypes and group provided (default =
  `"pattern"`).

- ...:

  arguments for
  [`ggplot_coef`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_coef.md)

- pattern:

  Use phenotype names as pattern.

## Value

object of class
[`ggplot`](https://ggplot2.tidyverse.org/reference/ggplot.html)

## Author

Brian S Yandell, <brian.yandell@wisc.edu>

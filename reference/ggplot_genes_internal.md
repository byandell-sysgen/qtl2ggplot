# GGPlot internal routine for ggplot_genes

Plot genes at positions

## Usage

``` r
ggplot_genes_internal(
  start,
  end,
  strand,
  rect_top,
  rect_bottom,
  colors,
  space,
  y,
  dir_symbol,
  name,
  xlim,
  xlab = "Position (Mbp)",
  ylab = "",
  bgcolor = "gray92",
  xat = NULL,
  legend.position = "none",
  vlines = NULL,
  ...
)
```

## Arguments

- start, end, strand, rect_top, rect_bottom, colors, space, y,
  dir_symbol, name, xlim:

  usual parameters

- legend.position, vlines, xlab, ylab, bgcolor, xat:

  hidden parameters

- ...:

  Additional graphics parameters.

## Value

object of class
[`ggplot`](https://ggplot2.tidyverse.org/reference/ggplot.html).
